---

layout: post
title:  "Repacking Android apps"

---
Repacking Android apps for testing

## Setup

On arch linux install android build tools from arch linux aur:
```
yay -S android-sdk-build-tools
```

get apktool from git or aur 

[apktool](https://github.com/iBotPeaches/Apktool)


## Unpacking
```
apktool d someapp.apk
```

## Repacking
```
mkdir keys
keytool -genkey -v -keystore keys/androidpersonal.keystore -alias AndroidPersonalKeystore -keyalg RSA -validity 1000
apktool b -o repacked.apk minecraft-1.20.20.20-apkmodget.com
zipalign -p -f -v 4 repacked.apk repacked_zipaligned_mc.apk
apksigner sign -ks ./keys/androidpersonal.keystore --v1-signing-enabled true --v2-signing-enabled true repacked_zipaligned_mc.apk
adb install repacked_zipaligned_mc.apk

```


