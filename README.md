New features
==============================
Since I mainly use this debugger to solve CTF pwnable challenges  
I add some features to help me analyze the binary more conveniently  
* `base`: Help me to set some images base address into variables, including `.text`, `libc.so` & `heap`  
```
gdb-peda$ base
setting $code = 0x7fa34bf5a000
setting $libc = 0x7fa34b456000
setting $heap = 0x7fa34d4be000
gdb-peda$ vmmap binary
Start              End                Perm      Name
0x00007fa34bf5a000 0x00007fa34bf6b000 r-xp      /home/bruce30262/practice/ghostparty/ghostparty
0x00007fa34c16a000 0x00007fa34c16b000 r-xp      /home/bruce30262/practice/ghostparty/ghostparty
0x00007fa34c16b000 0x00007fa34c16c000 rwxp      /home/bruce30262/practice/ghostparty/ghostparty
gdb-peda$ vmmap libc
Start              End                Perm      Name
0x00007fa34b456000 0x00007fa34b611000 r-xp      /lib/x86_64-linux-gnu/libc-2.19.so
0x00007fa34b611000 0x00007fa34b810000 ---p      /lib/x86_64-linux-gnu/libc-2.19.so
0x00007fa34b810000 0x00007fa34b814000 r-xp      /lib/x86_64-linux-gnu/libc-2.19.so
0x00007fa34b814000 0x00007fa34b816000 rwxp      /lib/x86_64-linux-gnu/libc-2.19.so
gdb-peda$ vmmap heap
Start              End                Perm      Name
0x00007fa34d4be000 0x00007fa34d4df000 rwxp      [heap]
gdb-peda$
```
* `canary`: Get the stack canary value. Store the value into `$canary` variable  
* `tls`: Show me some information of the `.tls` section, including memory maps, `$tls` ( tcbhead_t head address ) , `$canary` and the stack address lies in the `.tls` section  
```
gdb-peda$ tls
setting $tls = 0xf7e11940
setting $canary = 0xfc379800
0xf7e118c0:     0x00000000      0x00000000      0x00000000      0x00000000
0xf7e118d0:     0x00000000      0x00000000      0x00000000      0x00000000
0xf7e118e0:     0x00000000      0x00000000      0x00000000      0x00000000
0xf7e118f0:     0x00000000      0xf7fbc8a0      0xf7fbefc0      0x00000000
0xf7e11900:     0xf7f5d6a0      0xf7f5d0a0      0xf7f5dfa0      0x00000000
0xf7e11910:     0x00000000      0x00000000      0x00000000      0x00000000
0xf7e11920:     0x00000000      0x00000000      0x00000000      0x00000000
0xf7e11930:     0x00000000      0x00000000      0x00000000      0x00000000
0xf7e11940:     0xf7e11940      0xf7e11e08      0xf7e11940      0x00000000
0xf7e11950:     0xf7fdb420      0xfc379800      0xe61bcc32      0x00000000
0xf7e11960:     0x00000000      0x00000000      0x00000000      0x00000000
0xf7e11970:     0x00000000      0x00000000      0x00000000      0x00000000
0xf7e11980:     0x00000000      0x00000000      0x00000000      0x00000000
0xf7e11990:     0x00000000      0x00000000      0x00000000      0x00000000
0xf7e119a0:     0x00000000      0x00000000      0x00000000      0x00000000
0xf7e119b0:     0x00000000      0x00000000      0x00000000      0x00000000
0xf7e119c0:     0xffffd2c4      0x00000000      0x00000000      0x00000000
0xf7e119d0:     0x00000000      0x00000000      0x00000000      0x00000000
0xf7e119e0:     0x00000000      0x00000000      0x00000000      0x00000000
0xf7e119f0:     0x00000000      0x00000000      0x00000000      0x00000000
tls address: 0xf7e11940
stack canary: 0xfc379800
Found a stack address 0xffffd2c4 at 0xf7e119c0
gdb-peda$

```

Bug fixed
==========
I fixed some bug which exist in zachriggle's version of [peda](https://github.com/zachriggle/peda)  
* `elfsymbol`: Now able to list an ELF's symbols under Ubuntu Linux 14.04.1, Python3 + GDB 7.7.1  
```
gdb-peda$ elfsymbol
Found 23 symbols
memset@plt = 0x555555556560
close@plt = 0x555555556580
__gmon_start__@plt = 0x5555555565c0
setvbuf@plt = 0x555555556620
read@plt = 0x555555556640
__libc_start_main@plt = 0x555555556650
__cxa_atexit@plt = 0x555555556670
malloc_usable_size@plt = 0x5555555566a0
__cxa_finalize@plt = 0x5555555566d0
lseek@plt = 0x5555555566f0
signal@plt = 0x555555556710
memmove@plt = 0x555555556730
alarm@plt = 0x555555556750
__stack_chk_fail@plt = 0x555555556780
strcpy@plt = 0x5555555567b0
srand@plt = 0x5555555567f0
strncpy@plt = 0x555555556820
__sprintf_chk@plt = 0x555555556840
__gxx_personality_v0@plt = 0x555555556880
_Unwind_Resume@plt = 0x5555555568a0
memcpy@plt = 0x5555555568d0
open@plt = 0x5555555568e0
time@plt = 0x5555555568f0

gdb-peda$ elfsymbol strcpy
Detail symbol info
strcpy@reloc = 0x27
strcpy@plt = 0x5555555567b0
strcpy@got = 0x555555764ed8
gdb-peda$
```
* `asm-demangle`: Now able to demangle C++ symbols 
```
[---------------------------------------------------------stack----------------------------------------------------------]
00:0000| rsp 0x7fffffffe260 --> 0x7ffff7dd6f60 --> 0x7ffff7dbfd90 --> 0x7ffff7b695e0 (<_ZN9__gnu_cxx18stdio_sync_filebufIcSt11char_traitsIcEED1Ev>:       mov    rax,QWORD PTR [rip+0x259679]        # 0x7ffff7dc2c60)
01:0008|     0x7fffffffe268 --> 0x7ffff7b54093 (<_ZNSi6sentryC2ERSib+355>:      mov    rsi,QWORD PTR [rsp])
02:0016|     0x7fffffffe270 --> 0x7ffff7dd7800 --> 0x7ffff7dbf018 --> 0x7ffff7b53b60 (<_ZNSiD1Ev>:      mov    rax,QWORD PTR [rip+0x26f1d1]        # 0x7ffff7dc2d38)
03:0024|     0x7fffffffe278 --> 0xf78bb9e0
04:0032|     0x7fffffffe280 --> 0x7ffff7dd7800 --> 0x7ffff7dbf018 --> 0x7ffff7b53b60 (<_ZNSiD1Ev>:      mov    rax,QWORD PTR [rip+0x26f1d1]        # 0x7ffff7dc2d38)
05:0040|     0x7fffffffe288 --> 0x7fffffffe2ec --> 0x0
06:0048|     0x7fffffffe290 --> 0x7fffffffe2ec --> 0x0
07:0056|     0x7fffffffe298 --> 0x7ffff7b559cb (<_ZNSi10_M_extractIjEERSiRT_+27>:       cmp    BYTE PTR [rsp+0x1b],0x0)
[------------------------------------------------------------------------------------------------------------------------]
Legend: stack, code, data, heap, rodata, value
gdb-peda$ set print asm-demangle on
gdb-peda$ context stack
[---------------------------------------------------------stack----------------------------------------------------------]
00:0000| rsp 0x7fffffffe260 --> 0x7ffff7dd6f60 --> 0x7ffff7dbfd90 (:stdio_sync_filebuf<char, std::char_traits<char> >+16>:0x00007ffff7b695e0)
01:0008|     0x7fffffffe268 --> 0x7ffff7b54093 (<std::istream::sentry::sentry(std::istream&, bool)+355>:        mov    rsi,QWORD PTR [rsp])
02:0016|     0x7fffffffe270 --> 0x7ffff7dd7800 (:cin>:  0x00007ffff7dbf018)
03:0024|     0x7fffffffe278 --> 0xf78bb9e0
04:0032|     0x7fffffffe280 --> 0x7ffff7dd7800 (:cin>:  0x00007ffff7dbf018)
05:0040|     0x7fffffffe288 --> 0x7fffffffe2ec --> 0x0
06:0048|     0x7fffffffe290 --> 0x7fffffffe2ec --> 0x0
07:0056|     0x7fffffffe298 --> 0x7ffff7b559cb (<std::istream& std::istream::_M_extract<unsigned int>(unsigned int&)+27>:cmp    BYTE PTR [rsp+0x1b],0x0)
[------------------------------------------------------------------------------------------------------------------------]
Legend: stack, code, data, heap, rodata, value
gdb-peda$
```
* `start`: Now able to set breakpoint at specific entry point while running a **PIE** binary  




### ( The following readme are from the original README.md )


peda
====

PEDA - Python Exploit Development Assistance for GDB

## Screenshot
![start](http://i.imgur.com/f22ZRro.png)

## Enhancements:

 This version has been extended by Zach Riggle to add some features and give dual-compatibility with Python2 and Python3.

* Python2 and Python3 compatibility
* Line width wrapping on banners
* Colorize stack and heap differently than regular data
* Show registers alongside stack output (and 'telescope' command)
* Basic support for ARM and PPC registers
* Support for passing GDB variables to PEDA routines (e.g. `hexdump $pc`)

## Key Features:

These are the standard features of PEDA:

* Enhance the display of gdb: colorize and display disassembly codes, registers, memory information during debugging.
* Add commands to support debugging and exploit development (for a full list of commands use `peda help`):
  * `aslr` -- Show/set ASLR setting of GDB
  * `checksec` -- Check for various security options of binary
  * `dumpargs` -- Display arguments passed to a function when stopped at a call instruction
  * `dumprop` -- Dump all ROP gadgets in specific memory range
  * `elfheader` -- Get headers information from debugged ELF file
  * `elfsymbol` -- Get non-debugging symbol information from an ELF file
  * `lookup` -- Search for all addresses/references to addresses which belong to a memory range
  * `patch` -- Patch memory start at an address with string/hexstring/int
  * `pattern` -- Generate, search, or write a cyclic pattern to memory
  * `procinfo` -- Display various info from /proc/pid/
  * `pshow` -- Show various PEDA options and other settings
  * `pset` -- Set various PEDA options and other settings
  * `readelf` -- Get headers information from an ELF file
  * `ropgadget` -- Get common ROP gadgets of binary or library
  * `ropsearch` -- Search for ROP gadgets in memory
  * `searchmem|find` -- Search for a pattern in memory; support regex search
  * `shellcode` -- Generate or download common shellcodes.
  * `skeleton` -- Generate python exploit code template
  * `vmmap` -- Get virtual mapping address ranges of section(s) in debugged process
  * `xormem` -- XOR a memory region with a key

## Installation

    git clone https://github.com/zachriggle/peda.git ~/peda
    echo "source ~/peda/peda.py" >> ~/.gdbinit
    echo "DONE! debug your program with gdb and enjoy"
