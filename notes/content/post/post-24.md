---
title: "X86_64 stack frame in Assembly"
date: 2024-12-31T15:31:53+05:30
publishdate: 2024-12-31
lastmod: 2024-12-31
draft: false
tags: []
categories: ["linux"]
---
In **x86-64 assembly**, the **stack frame** is a key concept used to manage function calls, local variables, and arguments. The **RBP** (Base Pointer) and **RSP** (Stack Pointer) registers play a central role in this process.

### Stack Frame Overview

When a function is called, a **stack frame** is created to store:
1. The **return address** (the address to return to after the function finishes).
2. The **previous base pointer** (to restore the caller's stack frame).
3. **Local variables** for the current function.

### Key Registers:
- **RSP**: The stack pointer. It points to the top of the stack and changes as you push and pop values.
- **RBP**: The base pointer. It typically points to the base of the current function's stack frame, making it easier to access function arguments and local variables.
  
### Basic Steps for Creating a Stack Frame:
1. **Save the caller’s base pointer (`RBP`)**.
2. **Set the current base pointer (`RBP`)** to the current stack pointer (`RSP`) to mark the start of the new stack frame.
3. **Reserve space** for local variables by adjusting the stack pointer (`RSP`).
4. Use **`RBP`** to access function arguments and local variables.
5. At the end of the function, **restore** the old base pointer and **return**.

### Example: Creating a Stack Frame in a Function

Let's walk through an example where we:
- Push the return address and previous base pointer onto the stack.
- Create space for local variables.
- Use the base pointer (`RBP`) to access function arguments and local variables.

#### Example Code:
```asm
section .text
    global _start

_start:
    ; Call the function `my_function`
    call my_function

    ; Exit the program (using syscall)
    mov rax, 60          ; syscall number for exit
    xor rdi, rdi         ; exit code 0
    syscall

my_function:
    ; Prologue: Set up the stack frame
    push rbx              ; Save any registers we will use
    push rbp              ; Save the caller's base pointer (RBP)
    mov rbp, rsp          ; Set RBP to the current stack pointer (start of the new stack frame)

    ; Allocate space for local variables on the stack
    sub rsp, 16           ; Reserve 16 bytes for local variables (e.g., int vars)

    ; Access arguments passed to the function
    ; In the System V calling convention, the first argument is passed in RDI
    ; and the second argument in RSI.
    ; Let's assume we want to add them together.

    mov rax, rdi          ; Copy the first argument (RDI) to RAX
    add rax, rsi          ; Add the second argument (RSI) to RAX

    ; Store the result in a local variable (let's say we use 8 bytes)
    mov [rbp-8], rax      ; Store the result (from RAX) into the local variable at [rbp-8]

    ; Epilogue: Restore the stack frame
    mov rsp, rbp          ; Restore RSP to the value of RBP (remove local variables)
    pop rbp               ; Restore the caller's base pointer
    pop rbx               ; Restore the registers we saved
    ret                   ; Return to the caller

```

### Explanation:

1. **Function Prologue** (`my_function`):
   - `push rbx`: Saves the value of `RBX` (we're going to use it, so we save it to restore later).
   - `push rbp`: Saves the previous base pointer, which points to the caller's stack frame.
   - `mov rbp, rsp`: Sets the current function's base pointer (`RBP`) to the current stack pointer (`RSP`), marking the start of the new stack frame.

2. **Allocating Space for Local Variables**:
   - `sub rsp, 16`: Allocates 16 bytes of space for local variables. This is done by subtracting from `RSP`. In this case, we assume we need space for two `8-byte` local variables (e.g., integers, pointers).
   - After this, the stack pointer is adjusted, and the space is reserved below the `RBP` pointer.

3. **Accessing Function Arguments**:
   - In **System V AMD64** (used on Unix-like systems), the first two function arguments are passed in `RDI` and `RSI`.
   - `mov rax, rdi`: We copy the first argument (stored in `RDI`) into `RAX`.
   - `add rax, rsi`: We add the second argument (stored in `RSI`) to `RAX`.

4. **Storing Local Variables**:
   - `mov [rbp-8], rax`: We store the result of the addition (which is now in `RAX`) in the local variable at `[rbp-8]`. The local variable is stored relative to `RBP`, which makes it easy to access even after the stack pointer (`RSP`) changes.

5. **Function Epilogue**:
   - `mov rsp, rbp`: Restores the `RSP` to the value of `RBP`, which removes any local variables allocated in the stack frame.
   - `pop rbp`: Restores the caller's base pointer (`RBP`).
   - `pop rbx`: Restores the value of `RBX`.
   - `ret`: Pops the return address off the stack and returns to the caller.

### Stack Layout (Visualized):

Let’s visualize the stack at different points:

#### Just before calling `my_function`:
```
| ------------------------ | <- High memory
|  (return address)         |
|  (caller’s RBP)           |
| ------------------------ | <- Stack grows downward
|  (previous stack frame)   |
| ------------------------ | <- Low memory
```

#### After entering `my_function` (after prologue):
```
| ------------------------ | <- High memory
|  (return address)         |
|  (caller’s RBP)           |
| ------------------------ | <- RBP points here
|  (local variable space)   | <- RSP points here after `sub rsp, 16`
|  (saved registers)        |
| ------------------------ | <- Low memory
```

#### After returning from `my_function` (after epilogue):
```
| ------------------------ | <- High memory
|  (return address)         |
|  (caller’s RBP)           |
| ------------------------ | <- Stack restored to original state
| ------------------------ | <- Low memory
```

### Key Points:
- **Prologue**: Set up the stack frame by saving `RBP` and adjusting `RSP` to reserve space for local variables.
- **Arguments**: The first few arguments are passed in registers (e.g., `RDI`, `RSI`).
- **Local Variables**: You can use offsets from `RBP` to access local variables and saved registers.
- **Epilogue**: Clean up by restoring `RSP` and `RBP`, then return to the caller.

### Conclusion:

The **stack frame** in x86-64 is set up by saving the previous `RBP` (the caller's base pointer) and then creating space for local variables. This setup allows the function to access its arguments, local variables, and maintain a clean stack when returning to the caller. Using `RBP` to reference local variables and arguments relative to the stack helps maintain consistent and structured code in assembly.
