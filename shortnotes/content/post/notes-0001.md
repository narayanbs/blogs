---
title: "gcc compiler options when linking assembly files"
date: 2025-02-23T16:07:19+05:30
publishdate: 2025-02-23
lastmod: 2025-02-23
draft: true
tags: [assembly, gcc, linux]
categories: [coding]
---
for asm files with no c functions and with global _start  
`nasm -f elf64 ex06.asm && ld ex06.o -o ex06`

for asm files with c functions and with global main  
`nasm -f elf64 ex06.asm && gcc -no-pie ex06.o -o ex06`

for asm files with c functions and  _start instead of main  
`nasm -f elf64 ex06.asm && gcc -nostartfiles -no-pie ex06.o -o ex06`


Note: The reason for -no-pie while using gcc is because, 
Debian switched to PIC/PIE binaries in 64-bits mode and GCC by default will try to link the object as PIC.
(Position independent code/ Position independent executable).
But We use absolute addresses in our examples like mov rdi, message.. So gcc will complain and raise an error.
To prevent this we switchoff PIE by -no-pie flag. 


ex01.asm 
~~~bash
global main
extern puts

section .data
output: "hello world", 0h

section .text
main:
    mov rdi, output
    call puts

    ret
~~~
Now we compile the file using `nasm -f elf64 ex01.asm` and link it using `gcc -nopie ex01.o -o ex01`

ex02.asm
~~~bash
global _start
extern puts

section .data
output: "hello world", 0h

section .text
_start:
    mov rdi, output
    call puts

    mov rax, 60
    xor rdi, rdi
    syscall
~~~
Now we use `nasm -f elf64 ex02.asm` and link it using `gcc -no-pie -nostartfiles ex02.o -o ex02`

