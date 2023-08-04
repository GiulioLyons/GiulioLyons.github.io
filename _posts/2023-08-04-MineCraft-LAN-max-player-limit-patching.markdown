---

layout: post
title:  "MineCraft Android LAN max player limit bypass"

---
Patching MineCraft on Android to bypass the max player limit

```
apktool d minecraft.apk
```

If using a split apk the libs are located in `split_config.arm64_v8a.apk`

After unpacking navigate to the libs folder and find `libminecraftpe.so`

Using radare2 to patch the binary
```

r2 minecraft-1.20.20.20/lib/armeabi-v7a/libminecraftpe.so

 -- Move around the bytes with h,j,k,l! Arrow keys are neither portable nor efficient
[0x036948a0]> is~MaxPlayers
28875  0x037ff242 0x037ff242 GLOBAL FUNC   54         _ZNK11AppPlatform27getDefaultNetworkMaxPlayersEv
                                    AppPlatform::getDefaultNetworkMaxPlayers() const
[0x036948a0]> s 0x037ff242
[0x037ff242]> af
[0x037ff242]> pdf
┌ 54: sym.AppPlatform::getDefaultNetworkMaxPlayers___const (int16_t arg1);
│           ; arg int16_t arg1 @ r0
│           ; var int16_t var_8h @ sp+0x8
│           0x037ff242      d0b5           push {r4, r6, r7, lr}       ; AppPlatform::getDefaultNetworkMaxPlayers() const
│           0x037ff244      02af           add r7, var_8h
│           0x037ff246      0446           mov r4, r0                  ; arg1
│           0x037ff248      0068           ldr r0, [r0]                ; arg1
│           0x037ff24a      d0f82013       ldr.w r1, [r0, 0x320]       ; arg1
│           0x037ff24e      2046           mov r0, r4
│           0x037ff250      8847           blx r1
│           0x037ff252      0128           cmp r0, 1
│       ┌─< 0x037ff254      0ed0           beq 0x37ff274
│       │   0x037ff256      2068           ldr r0, [r4]
│       │   0x037ff258      d0f86413       ldr.w r1, [r0, 0x364]
│       │   0x037ff25c      2046           mov r0, r4
│       │   0x037ff25e      8847           blx r1
│       │   0x037ff260      0023           movs r3, 0
│       │   0x037ff262      0022           movs r2, 0
│       │   0x037ff264      c9f64043       movt r3, 0x9c40
│       │   0x037ff268      181a           subs r0, r3, r0
│       │   0x037ff26a      72eb0100       sbcs.w r0, r2, r1
│      ┌──< 0x037ff26e      01d2           bhs 0x37ff274
│      ││   0x037ff270      0820           movs r0, 8
│      ││   0x037ff272      d0bd           pop {r4, r6, r7, pc}
│      └└─> 0x037ff274      0520           movs r0, 5                  
└           0x037ff276      d0bd           pop {r4, r6, r7, pc}
[0x037ff242]>
```

at `0x037ff274` here is the 5 player max limit

```
[0x037ff242]> s 0x037ff274
[0x037ff274]> oo+
[0x037ff274]> wa movs r0, 100
INFO: Written 2 byte(s) (movs r0, 100) = wx 6420 @ 0x037ff274
```
Print to see the modification

```
[0x037ff274]> pd1
│           0x037ff274      6420           movs r0, 0x64               ; 'd'
[0x037ff274]>
```


