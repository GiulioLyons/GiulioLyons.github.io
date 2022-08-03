---

layout: post
title:  "TryCrackMe"

---

## TryCrackMe
[MrEmpy's TryCrackMe](https://crackmes.one/crackme/61c8deff33c5d413767ca0ea)

Open the binary with radare2
```
Ξ ~/crackmes → r2 trycrackme
Warning: run r2 with -e bin.cache=true to fix relocations in disassembly
 -- Documentation is for weak people.
[0x000010a0]> aa
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze all functions arguments/locals
[0x000010a0]> s main
```

Set a breakpoint at 0x000012d3 `db 0x000012d3`
```

0x000012c8      488d4d90       lea rcx, [var_70h]
0x000012cc      488d8550ffff.  lea rax, [var_b0h]
0x000012d3 b    4889ce         mov rsi, rcx
0x000012d6      4889c7         mov rdi, rax
0x000012d9      e852fdffff     call sym.imp.strncmp 
```

Reopen the binary in debug mode `ood`
```
> ood
File dbg:///home/w00t/crackmes/trycrackme reopened in read-write mode
```
Continue until breakpoint `dc`

```
> dc

  _____          ___             _   __  __
 |_   _| _ _  _ / __|_ _ __ _ __| |_|  \/  |___
   | || '_| || | (__| '_/ _` / _| / / |\/| / -_)
   |_||_|  \_, |\___|_| \__,_\__|_\_\_|  |_\___|
           |__/

Put the key: 999999
hit breakpoint at: 0x55ba8a93f2d3
```

Show register refrences `drr`
```
> drr
role reg     value            refstr
――――――――――――――――――――――――――――――――――――
     riz     0                0
R0   rax     7ffe8b609db0     [stack] rax stack R W 0x393939393939 999999
     rbx     7ffe8b609f78     [stack] rbx stack R W 0x7ffe8b60b5e5
A3   rcx     7ffe8b609df0     [stack] rcx,rdi stack R W 0x3337333730343433 34407373373234353336
A2   rdx     14               20 rdx
A4   r8      7fddabb3c8c0     r8 0123456789abcdefghijklmnopqrstuvwxyz
A5   r9      78               120 r9 ascii ('x')
     r10     7ffe8b609b16     [stack] r10 stack R W 0xe8a78f503e003633 36
     r11     0                0
     r12     0                0
     r13     7ffe8b609f88     [stack] r13 stack R W 0x7ffe8b60b604
     r14     0                0
     r15     7fddabbd6000     /usr/lib/ld-linux-x86-64.so.2 r15 library R W 0x7fddabbd72a0
A1   rsi     55ba8a94012b     /home/w00t/crackmes/trycrackme .rodata rsi program R 0x636e49205d2d5b00
A0   rdi     7ffe8b609df0     [stack] rcx,rdi stack R W 0x3337333730343433 34407373373234353336
SP   rsp     7ffe8b609d80     [stack] rsp stack R W 0x7ffe8b609f78
BP   rbp     7ffe8b609e60     [stack] rbp stack R W 0x1
PC   rip     55ba8a93f2d3     /home/w00t/crackmes/trycrackme .text rip main program R X 'mov rsi, rcx' 'trycrackme'
     cs      33               51 ascii ('3')
     rflags  202              514 rflags
SN   orax    ffffffffffffffff
     ss      2b               43 ascii ('+')
     fs      7fddab97a740
     gs      0                0
     ds      0                0
     es      0                0
     fs_base 0                0
     gs_base 0                0
```

Password is in rcx

Solution: `34407373373234353336`

```
Ξ ~/crackmes → ./trycrackme

  _____          ___             _   __  __
 |_   _| _ _  _ / __|_ _ __ _ __| |_|  \/  |___
   | || '_| || | (__| '_/ _` / _| / / |\/| / -_)
   |_||_|  \_, |\___|_| \__,_\__|_\_\_|  |_\___|
           |__/

Put the key: 34407373373234353336
[+] Correct key!%
```
