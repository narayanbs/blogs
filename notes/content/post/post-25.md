---
title: "Linker 1"
date: 2025-01-16T08:06:55+05:30
publishdate: 2025-01-16
lastmod: 2025-01-16
draft: false
tags: []
categories: []
---
Yes, you're on the right track. Here's what's happening with your NASM assembly and how the PIE (Position-Independent Executable) issue is related:

### Understanding PIE and PLT

When you compile with **PIE** (Position-Independent Executable) enabled, the executable code is designed to work at any memory address, so the addresses used by functions (like `puts`) cannot be hardcoded into the binary. Instead, the addresses of external functions are determined at runtime, which is why the **Procedure Linkage Table (PLT)** is used.

- The **PLT** contains stubs that will resolve the address of an external function (such as `puts`) when it's first called, through dynamic linking at runtime.
- The **GOT** (Global Offset Table) stores the actual address of the function once it has been resolved.

With **PIE** enabled, `gcc` expects the call to `puts` to go through the **PLT** (i.e., `puts@plt`), so the code is not using an absolute address, but rather an indirect jump to the PLT entry that will eventually resolve to the correct address of the function at runtime.

### What’s Happening with Your NASM Code

In your NASM code, you have:
```asm
extern puts
call puts
```

Here, **NASM** is generating an absolute address for the `puts` function in the assembly (i.e., it’s just inserting a direct call to `puts` assuming it's already linked properly).

However, when linking with **PIE** enabled, `puts` is **not an absolute address**. Instead, the address of `puts` is resolved at runtime, and the call should go through the PLT. GCC expects to generate `call puts@plt` instead, to go through the dynamic linker at runtime.

### Why You Need `-no-pie`

When you compile without `-no-pie`, `gcc` expects all the calls to external functions to go through the PLT, which involves generating code like `call puts@plt`. Since you're not using the PLT in your NASM code (`call puts`), you're encountering a mismatch.

- **PIE** (Position Independent Executable) requires that function calls to external symbols (like `puts`) go through the **PLT**, so that the address can be resolved at runtime.
- If you don't use the **PLT**, your call is essentially trying to use an absolute address, but this is not feasible in a PIE binary, leading to the issue.
  
By passing `-no-pie` to GCC, you're disabling PIE, and the code can use absolute addresses (i.e., the `call puts` in your NASM code would work because there are no runtime address resolutions required).

### Conclusion

In short:
- Your NASM code is using a direct call to `puts`, which assumes an absolute address.
- With **PIE** enabled, you need to use `puts@plt` for runtime address resolution.
- The `-no-pie` flag disables PIE, allowing your code to use absolute addresses and bypass the need for the PLT.

If you want to use PIE, you'll need to modify your assembly to call `puts@plt` instead of just `puts`. Alternatively, as you noted, using `-no-pie` allows the absolute address approach.

---------------------------------------

Yes, you're absolutely right! Even when using an absolute address in the NASM object file, the address still needs to be **relocated** when linking the object file into the final executable. Let's break this down a bit further:

### Absolute Address vs. Relocation

1. **NASM Object File**:
   - In your NASM assembly, when you write `call puts`, it generates a direct call to the `puts` symbol, which, at this point, will have a **placeholder** for the address of `puts`. This placeholder is a **symbolic reference** to `puts`, which needs to be resolved by the linker.
   - The address of `puts` is not yet set when you generate the object file; it's still a **symbolic reference** that will require **relocation** when linking the object file to create the final executable.

2. **Linking and Relocation**:
   - When you link the object file to produce the final executable (whether it’s PIE or not), the **linker** needs to resolve the address of `puts`.
   - If you are linking with **PIE** enabled, the final executable needs to be position-independent. This means that the linker cannot simply hardcode an absolute address for `puts` because the code will run at a variable address, and that address can only be resolved at runtime.
   - The **relocation** process is what happens during linking, where the linker will replace the symbolic reference (`puts`) with an actual address. In the case of PIE, the linker will create an entry in the **GOT (Global Offset Table)** and the call to `puts` will be redirected to the **PLT (Procedure Linkage Table)**.
     - The first time `puts` is called, it will go through the PLT to resolve the address of `puts` in the GOT.

3. **If You Use `-no-pie`**:
   - If you link with `-no-pie`, then the final executable is **not** position-independent, and the linker can simply replace the `puts` reference with an **absolute address**. In this case, you don’t need the PLT/GOT mechanism.
   - However, the address is still resolved by the linker, but since the executable is not PIE, the linker can use a fixed address (instead of resolving it dynamically at runtime) because the binary is not expected to be loaded at any address, so it doesn’t need to be position-independent.

### In Summary:

- When you assemble and link your NASM code, **relocation is still necessary** for resolving the address of `puts` in the final executable.
  - With **PIE** enabled, this relocation is handled dynamically at runtime, and the call must go through the **PLT** to resolve the address.
  - With **-no-pie**, the relocation is handled statically at link time, and the call can directly use the absolute address of `puts`.

So, **yes**—even if you use an "absolute address" in your object file (i.e., directly referencing `puts`), the linker must still **relocate** it in the final executable. This is true whether you are using PIE or not, but PIE introduces the complexity of having to use the PLT for dynamic resolution.


-----------------------

Yes, we can draw a parallel between the **load-time relocation** typically seen in **32-bit systems** and the **relocation process** that occurs when linking a **Position-Independent Executable (PIE)**. However, there are important differences between how relocation works in 32-bit systems and in PIE executables, especially in the context of modern 64-bit systems.

### Load-Time Relocation in 32-bit Systems

In **32-bit systems**, executable files (especially those that are not dynamically linked) require **load-time relocation**. This means that:

- When the executable is loaded into memory, the operating system or loader must adjust addresses in the code and data sections.
- This is because the address of the executable’s sections (like `.text`, `.data`, etc.) could change each time it is loaded, as it's loaded into different memory locations by the operating system.
- The addresses used in the code need to be adjusted at **runtime** by the loader to reflect the actual memory locations where the sections of the executable are loaded.
  
The loader performs the necessary **relocation** by updating the absolute addresses in the binary with the base address at which the executable is loaded.

### Relocation in PIE Executables

For **Position-Independent Executables (PIE)**, the situation is slightly different, but there are similarities:

- **PIE executables** are designed to be **position-independent**, meaning they can be loaded at any memory address. This allows for **dynamic loading**, which is particularly useful in environments where multiple processes share the same library code (like in shared libraries).
  
- **Relocation for PIE** happens during the **linking process** and is primarily for symbol resolution, not for adjusting memory addresses directly, as in 32-bit systems. The important mechanisms here are:
  - **PLT (Procedure Linkage Table)** and **GOT (Global Offset Table)**: These structures are used for **indirect address resolution** at runtime. The first time an external function (like `puts`) is called, the address is looked up through the PLT, which then dynamically resolves the address of `puts` via the dynamic linker.
  - **GOT entries** contain the actual addresses of symbols once they are resolved. If the binary is loaded at a different memory address than expected, the relocation mechanism ensures that function calls and data accesses are redirected to the correct addresses in memory.

So, in PIE, the relocation process is **load-time resolution of function addresses** (via PLT/GOT) when the program is executed, and the addresses are not fixed at link time but are resolved when the executable is loaded into memory.

### Comparison to 32-bit Load-Time Relocation

- **32-bit system relocation** involves **direct address modification** by the loader when the program is loaded into memory. The loader adjusts the program’s absolute addresses based on the address where the program is loaded.
  
- **PIE relocation** involves **runtime address resolution** (via PLT/GOT) for external function calls. The executable is **relocated at load time** in the sense that its dynamic symbols (such as `puts`) are resolved when the program is loaded and executed. This is managed by the dynamic linker/loader, which may adjust addresses in the **GOT**.

### Key Differences

- **32-bit systems** use **load-time relocation** to modify the program’s addresses based on where the program is loaded into memory.
- **PIE** uses **runtime relocation** for external symbols, typically involving the **PLT** and **GOT** to resolve addresses at runtime when the program starts executing.

### Conclusion

Yes, in both 32-bit systems and PIE executables, **relocation** happens at **load time** when the program is loaded into memory, but the nature of the relocation differs:

- In **32-bit systems**, the relocation adjusts the memory addresses directly in the program’s sections.
- In **PIE executables**, the relocation typically involves resolving symbols dynamically via PLT/GOT at runtime, not adjusting absolute addresses directly in the binary.

In that sense, while both are **load-time relocations**, the exact mechanism and the need for runtime resolution in PIE executables is a bit more sophisticated than the traditional relocation seen in 32-bit binaries.
