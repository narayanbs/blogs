---
title: "GNU Binutils"
date: 2023-07-13T14:28:59+05:30
publishdate: 2023-07-13
lastmod: 2023-07-13
draft: true
tags: [Linux]
categories: []
---
GNU Binutils are a collection of binary tools, like 
* ld - the gnu linker
* as - the gnu assembler
* nm - Lists symbols from object files.
* objdump - Displays information from object files.
* readelf - Displays information from any ELF format object file.
* size - Lists the section sizes of an object or archive file.
* ar - A utility for creating, modifying and extracting from archives.

Few others can also be found at `https://www.gnu.org/software/binutils/`

Let's have a look at a few here.

**objdump** - Displays information from object files.
```
objdump -d <<execfile>>

for ex:
$ objdump -d python3.4

python3.4:     file format elf64-x86-64

Disassembly of section .init:

0000000000419558 <_init>:
  419558:	48 83 ec 08          	sub    $0x8,%rsp
  41955c:	e8 9b 14 00 00       	callq  41a9fc <call_gmon_start>
  419561:	48 83 c4 08          	add    $0x8,%rsp
  419565:	c3                   	retq   

Disassembly of section .plt:

0000000000419570 <wcscat@plt-0x10>:
  419570:	ff 35 92 0a 4e 00    	pushq  0x4e0a92(%rip)        # 8fa008 <_GLOBAL_OFFSET_TABLE_+0x8>
  419576:	ff 25 94 0a 4e 00    	jmpq   *0x4e0a94(%rip)        # 8fa010 <_GLOBAL_OFFSET_TABLE_+0x10>
  41957c:	0f 1f 40 00          	nopl   0x0(%rax)

0000000000419580 <wcscat@plt>:
  419580:	ff 25 92 0a 4e 00    	jmpq   *0x4e0a92(%rip)        # 8fa018 <_GLOBAL_OFFSET_TABLE_+0x18>
  419586:	68 00 00 00 00       	pushq  $0x0
  41958b:	e9 e0 ff ff ff       	jmpq   419570 <_init+0x18>

  ....
```
Read the man pages for more information `man objdump`

**nm** - Lists symbols from object files
```
nm -l <<execfile>>

for ex:
$ nm -l python3.4

00000000004259c9 T PyRun_SimpleString	/tmp/Python-3.4.0rc1/Python/pythonrun.c:2969
0000000000421d36 T PyRun_SimpleStringFlags	/tmp/Python-3.4.0rc1/Python/pythonrun.c:1629
0000000000425993 T PyRun_String	/tmp/Python-3.4.0rc1/Python/pythonrun.c:2962
0000000000423afc T PyRun_StringFlags	/tmp/Python-3.4.0rc1/Python/pythonrun.c:2091
0000000000496b94 T PySeqIter_New	/tmp/Python-3.4.0rc1/Objects/iterobject.c:12
000000000091a6e0 D PySeqIter_Type	/tmp/Python-3.4.0rc1/Objects/iterobject.c:131
0000000000463e13 T _PySequence_BytesToCharpArray	/tmp/Python-3.4.0rc1/Objects/abstract.c:2698
000000000045fe72 T PySequence_Check	/tmp/Python-3.4.0rc1/Objects/abstract.c:1358
000000000045ff5f T PySequence_Concat	/tmp/Python-3.4.0rc1/Objects/abstract.c:1393
000000000046146f T PySequence_Contains	/tmp/Python-3.4.0rc1/Objects/abstract.c:1875
0000000000461445 T PySequence_Count	/tmp/Python-3.4.0rc1/Objects/abstract.c:1866
0000000000460823 T PySequence_DelItem	/tmp/Python-3.4.0rc1/Objects/abstract.c:1571
```
Read the man pages for more information `man nm`

**readelf** - Displays information from elf format file
```
readelf -all <<execfile>>

for ex:
$ readelf -all python3.4
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00 
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x41a9d0
  Start of program headers:          64 (bytes into file)
  Start of section headers:          6131376 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         9
  Size of section headers:           64 (bytes)
  Number of section headers:         38
  Section header string table index: 35

  ...
```



