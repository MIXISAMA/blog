---
title: VSCode 进阶配置之 Include
date: 2020-11-24T00:25:46+08:00
categories: 
- VSCode
tags:
- VSCode
- acm
---

## 1 简述

随着我们自定义的宏函数（比如LOG）越来越多，代码头也会变得越来越臃肿。所以可以将一些不需要上传到oj上的代码，扔进单独的文件中。主文件include这些代码即可。同时还要引入万能头文件，进一步减少代码臃肿程度。

<!-- more -->

## 2 创建自己的include目录

在工程根目录下创建`include`目录，在里面创建`mylog.h`文件。在此文件中添加如下代码。

```C++
#pragma once
#include <iostream>
#include <cstdio>
using namespace std;
#define LOG(format, ...) {                                                     \
    printf("[%s:%d] " format "\n", __FUNCTION__, __LINE__, ##__VA_ARGS__);     \
}
#define LOGV(v) {                                                              \
    printf("[%s:%d] " #v " [ ", __FUNCTION__, __LINE__);                       \
    for(auto a:v) cout<<a<<", ";                                               \
    if(!v.empty()) printf("\b\b");                                             \
    printf(" ]\n");                                                            \
}
#define LOGM(m) {                                                              \
    printf("[%s:%d] " #m " { ", __FUNCTION__, __LINE__);                       \
    for(auto a:m) cout<<a.first<<": "<<a.second<<", ";                         \
    if(!m.empty()) printf("\b\b");                                             \
    printf(" }\n");                                                            \
}
```

## 3 配置include目录

### 3.1 tasks.json

在args中添加`-I`，`${workspaceFolder}/include/`参数。

```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: Debug",
            "command": "g++.exe",
            "args": [
                "-D__DEBUG__",
                "-I",
                "${workspaceFolder}/include/",
                "-g",
                "${file}",
                "-o",
                "${fileDirname}\\${fileBasenameNoExtension}.exe"
            ],
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "detail": "Generated task by Debugger"
        }
    ],
    "version": "2.0.0"
}
```

### 3.2 settings.json

在`C_Cpp.default.includePath`中，添加自己的include目录（mylog.h在里面）,还有MinGW的include目录（万能头文件在里面）。

{
    "C_Cpp.default.cppStandard": "c++14",
    "files.encoding": "gbk",
    "C_Cpp.default.includePath": [
        "${workspaceFolder}/include/**",
        "D:/MinGW/lib/gcc/mingw32/9.2.0/include/c++/**"
    ]
}

## 4 Sample

可以看到代码头没那么臃肿了。

```C++
#include <bits/stdc++.h>
// using namespace std;
// #define __DEBUG__
#ifdef __DEBUG__
#include "mylog.h"
#else
#define LOG(format, ...)
#define LOGV(v)
#define LOGM(m)
#endif

int main(void){
    LOG("123%d", 4);
    return 0;
}
```
