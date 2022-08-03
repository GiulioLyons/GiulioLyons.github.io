---

layout: post
title:  "Simple License Checkers"

---
Solving simple license checkers with radare2

## license checker 0x01
[NomanProdhan's License Checker 0x01](https://crackmes.one/crackme/619eda7b33c5d455dece628d)

```
r2 license_checker_1
```
print symbols with `is`

![alt text](/assets/license_checker_1/2022-08-02-143110.png "Symbols")

Seek to the symbol main `s main`

Analyze the function with `af`

Print the function with `pdf`

![alt text](/assets/license_checker_1/2022-08-02-143145.png "print main")

Inside main you can see the string it loads to compare against:
```assembly
lea rdx, str.KS_LICENSE_KEY_2021_REV_1 ; 0x2027 ; "KS-LICENSE-KEY-2021-REV-1"
mov rsi, rdx
mov rdi, rax
call sym.imp.strcmp         ; int strcmp(const char *s1, const char *s2)
```
Solution: `./license_checker_1 KS-LICENSE-KEY-2021-REV-1`


## license checker 0x02
[NomanProdhan's License Checker 0x02](https://crackmes.one/crackme/61c62bde33c5d413767ca0a0)

Running the program with no args outputs:
`Usage : %s <your name here> <license key here>`

```
r2 license_checker_2
```
Check strings inside the binary with the `iz` command:
```
[0x0000115a]> iz
[Strings]
nth paddr      vaddr      len size section type  string
―――――――――――――――――――――――――――――――――――――――――――――――――――――――
0   0x00002008 0x00002008 47  48   .rodata ascii Usage : %s <your name here> <license key here>\n
1   0x00002038 0x00002038 12  13   .rodata ascii NomanProdhan
2   0x00002045 0x00002045 25  26   .rodata ascii KS-LICENSE-KEY-2021-REV-2
3   0x00002060 0x00002060 64  65   .rodata ascii Congratulations %s, premium service has been activated for you.\n
4   0x000020a8 0x000020a8 39  40   .rodata ascii Sorry ! %s , %s is an invalid license.\n
5   0x000020d0 0x000020d0 39  40   .rodata ascii Sorry ! %s , you don't have a license.\n
```
Check for symbols with the `is` command and grep with `~` for `main`

Command: `is~main`
```
38  0x00001159 0x00001159 GLOBAL FUNC   259      main
3   0x00000000 0x00000000 GLOBAL FUNC   16       imp.__libc_start_main
```
Seek to main function:
```
s main
```
Grep for strcmp in the main:
```
[0x0000115a]> pdf~strcmp
0x000011ae      e88dfeffff     call sym.imp.strcmp         ; int strcmp(const char *s1, const char *s2)
0x000011cf      e86cfeffff     call sym.imp.strcmp         ; int strcmp(const char *s1, const char *s2)
```
The first strcmp:
```
0x000011a1      488d15900e00.  lea rdx, str.NomanProdhan   ; 0x2038 ; "NomanProdhan"
0x000011a8      4889d6         mov rsi, rdx
0x000011ab      4889c7         mov rdi, rax
0x000011ae      e88dfeffff     call sym.imp.strcmp         ; int strcmp(const char *s1, const char *s2)
```


Second strcmp:
```
0x000011c2      488d157c0e00.  lea rdx, str.KS_LICENSE_KEY_2021_REV_2 ; 0x2045 ; "KS-LICENSE-KEY-2021-REV-2"
0x000011c9      4889d6         mov rsi, rdx
0x000011cc      4889c7         mov rdi, rax
0x000011cf      e86cfeffff     call sym.imp.strcmp         ; int strcmp(const char *s1, const char *s2)
```

Solution:
`./license_checker_2 NomanProdhan KS-LICENSE-KEY-2021-REV-2`



## license checker 0x03
[NomanProdhan's License Checker 0x03](https://crackmes.one/crackme/62072dd633c5d46c8bcbfd9b)

`r2 license_checker_3`
Analyze symbols with `aa`

Print strings:
```
[0x00001179]> iz
[Strings]
nth paddr      vaddr      len size section type  string
―――――――――――――――――――――――――――――――――――――――――――――――――――――――
0   0x00002008 0x00002008 51  52   .rodata ascii Usage : %s <license pass code here [numbers only]>\n
1   0x00002040 0x00002040 35  36   .rodata ascii Premium access has been activated !
2   0x00002064 0x00002064 18  19   .rodata ascii Wrong license code
```

Seek to main `s main`

Print summary of function with `pdfs`:
```
0x00001181 argc
0x00001184 argv
0x000011a7 str.Usage_:__s__license_pass_code_here__numbers_only___n
0x000011ae str.Usage_:__s__license_pass_code_here__numbers_only___n
0x000011b6 call sym.imp.printf
0x000011c0 call sym.imp.exit
0x000011c5:
0x000011d5:
0x000011f5 call sym.imp.atoi
0x00001201:
0x0000120f call sym.imp.strlen
0x0000121f str.Premium_access_has_been_activated__
0x00001226 str.Premium_access_has_been_activated__
0x00001229 call sym.imp.puts
0x00001233 call sym.imp.exit
0x00001238:
0x00001238 str.Wrong_license_code
0x0000123f str.Wrong_license_code
0x00001242 call sym.imp.puts
0x0000124c call sym.imp.exit
```

Loop from main:
```

┌─> 0x55b204db41d5      488b45d0       mov rax, qword [var_30h]
╎   0x55b204db41d9      4883c008       add rax, 8
╎   0x55b204db41dd      488b10         mov rdx, qword [rax]
╎   0x55b204db41e0      8b45f4         mov eax, dword [var_ch]
╎   0x55b204db41e3      4898           cdqe
╎   0x55b204db41e5      4801d0         add rax, rdx
╎   0x55b204db41e8      0fb600         movzx eax, byte [rax]
╎   0x55b204db41eb      8845ef         mov byte [var_11h], al
╎   0x55b204db41ee      488d45ef       lea rax, [var_11h]
╎   0x55b204db41f2      4889c7         mov rdi, rax
╎   0x55b204db41f5      e866feffff     call sym.imp.atoi       ;[1] ; int atoi(const char *str)
╎   0x55b204db41fa      0145f0         add dword [var_10h], eax // add the value returned from atoi
╎   0x55b204db41fd      8345f401       add dword [var_ch], 1 	// add to the loop counter
╎   0x55b204db4201      488b45d0       mov rax, qword [var_30h]
╎   0x55b204db4205      4883c008       add rax, 8
╎   0x55b204db4209      488b00         mov rax, qword [rax]
╎   0x55b204db420c      4889c7         mov rdi, rax
╎   0x55b204db420f      e82cfeffff     call sym.imp.strlen     ;[2] ; size_t strlen(const char *s)
╎   0x55b204db4214      3945f4         cmp dword [var_ch], eax // check has looped strlen of argv[1]
└─< 0x55b204db4217      7cbc           jl 0x55b204db41d5
    0x55b204db4219      837df032       cmp dword [var_10h], 0x32 // check if var_10h equals 0x32
┌─< 0x55b204db421d      7519           jne 0x55b204db4238
│   0x55b204db421f      488d051a0e00.  lea rax, str.Premium_access_has_been_activated__    ; 0x55b204db5040 ; "Premium access has been activated !"
```
`var_ch = loop counter`

`var_10h = sum`

loops the length of input

converts ascii to int

adds the int in var_10h

checks if looped strlen of input

checks if var_10h is equal to 0x32

Solution:
Sum of all digits equal to 50
```
./license_checker_3 5555555555
Premium access has been activated !
```
