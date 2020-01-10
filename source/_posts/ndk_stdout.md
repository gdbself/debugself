---
title: Android NDK错误cannot locate symbol "stdout" referenced
tags:
  - Android
  - NDK
url: 130.html
id: 130
categories:
  - Android
keywords: Android,NDK
description: 'Android NDK提示cannot locate symbol "stdout" referenced错误的解决方法，APP_PLATFORM版本的含义'
date: 2018-01-05 00:43:21
---

## 问题描述
NDK r15b编译的app，在Android 6.0上正确运行，在低于Android 6.0的系统上(如Android 3.0，Android 4.0，Android 5.0)直接闪退。调试时logcat提示

dlopen("/data/app/com.debugself.org-1/lib/arm/libMyGame.so", RTLD_LAZY) failed: dlopen failed: cannot locate symbol "stdout" referenced by "libMyGame.so"


## 解决方法:定义APP_PLATFORM低于23

### APP_PLATFORM的含义

APP\_PLATFORM指定了编译链接，以及运行时链接的C/C++库（如libc,libstdc++等）的版本；比如APP\_PLATFORM为14时，编译时链接的是ndk-14版本对应的C/C++库，当编译好的程序运行在安卓手机时，即使手机版本为android-23，程序运行链接的依旧是ndk-14版本对应的C/C++库。

### 解决办法

本来网上找到解决方案，在Appication.mk里第一行加入 APP\_PLATFORM := android-19  不行的话再试试 改成 APP\_PLATFORM := android-9 再不行就多换几个ndk版本试试这个方法。

可惜很多方法我试了个遍都不行，后来发现在build.gradle中
```
arguments 'APP\_PLATFORM=android-'+PROP\_APP_PLATFORM
```
而PROP\_APP\_PLATFORM在gradle.properties中定义为
```
PROP\_APP\_PLATFORM=23
```
原来Appication.mk中定义的 APP\_PLATFORM被build.gradle定义的APP\_PLATFORM覆盖了，修改build.gradle定义的APP_PLATFORM低于23即可

## 原因分析

通过nm工具查看libMyGame.so，发现里面需要符号stdout，stderr，而在"\\ndk\\platforms\\android-23\\arch-arm\\usr\\lib\\libc.so"中，是可以查看到stdout这个符号的，但是在低于android-23版本的libc中是没有这个符号的。我们再查找stdout的定义，在"\\ndk\\sysroot\\usr\\include\\stdio.h"中发现如下定义：

```
#if \_\_ANDROID\_API__ >= \_\_ANDROID\_API\_M\_\_

extern FILE* stdin \_\_INTRODUCED\_IN(23);
extern FILE* stdout \_\_INTRODUCED\_IN(23);
extern FILE* stderr \_\_INTRODUCED\_IN(23);

/\* C99 and earlier plus current C++ standards say these must be macros. */

#define stdin stdin
#define stdout stdout
#define stderr stderr

#else

/\* Before M the actual symbols for stdin and friends had different names. */

extern FILE \_\_sF\[\] \_\_REMOVED_IN(23);

#define stdin (&__sF\[0\])
#define stdout (&__sF\[1\])
#define stderr (&__sF\[2\])

#endif
```
其中\_\_ANDROID\_API\_M\_\_就是23，对应了android-23；而\_\_ANDROID\_API__是来自APP\_PLATFORM中的版本号（通过一步步分析"ndk\\build\\ndk-build.cmd"这个脚本得出的结果）；通过上面的代码，android-23版本后stdout是一个变量符号，而低于android-23的版本中，stdout被宏定义替换为(&\_\_sF\[1\])，所以低于android-23的系统中，是不需要stdout这个符号的（因为stdout编译后被宏替换为&__sF\[1\]），当前低于android-23的系统中也没有stdout这个符号。


**综上所述，为了兼容低于android-23的系统，ndk编译的程序，需要设置APP_PLATFORM小于23**