---
title: VSCode 进阶配置之 Log
date: 2020-11-24T00:15:33+08:00
categories: 
- VSCode
tags:
- VSCode
- acm
---

## 1 简述

之前只说到了如何用vscode对c++代码Debug，但是Debug对于stl容器并不友好，我们可以自定义一些LOG对stl容器进行输出。

<!-- more -->

## 2 代码


```C++
#include <iostream>
#include <vector>
#include <map>
using namespace std;

// #define __DEBUG__

#ifdef __DEBUG__
#define LOG(format, ...) {                                                          \
    printf("[%s:%d] " format "\n", __FUNCTION__, __LINE__, ##__VA_ARGS__);          \
}
#define LOGV(v) {                                                                   \
    printf("[%s:%d] " #v " [ ", __FUNCTION__, __LINE__);                            \
    for(auto a:v) cout<<a<<", ";                                                    \
    if(!v.empty()) printf("\b\b");                                                  \
    printf(" ]\n");                                                                 \
}
#define LOGM(m) {                                                                   \
    printf("[%s:%d] " #m " { ", __FUNCTION__, __LINE__);                            \
    for(auto a:m) cout<<a.first<<": "<<a.second<<", ";                              \
    if(!m.empty()) printf("\b\b");                                                  \
    printf(" }\n");                                                                 \
}
#else
#define LOG(format, ...)
#define LOGV(v)
#define LOGM(m)
#endif

int main(void){
    LOG("12%d", 3);

    vector<string> vc;
    vc.push_back("abc");
    vc.push_back("xyz");
    vc.push_back("jqk");
    LOGV(vc);

    map<string, int> mp;
    mp["123"] = 10;
    mp["abc"] = 100;
    mp["678"] = 1000;
    LOGM(mp);

    return 0;
}
```

注意看宏定义部分，逻辑就是当程序宏定义了__DEBUG__，LOG函数就会生效进行输出。对于每一个单独cpp文件，都可以把这一段宏定义加上，在关键处进行输出。此代码输出结果如下。

```txt
[main:31] 123
[main:37] vc [ abc, xyz, jqk ]
[main:43] mp { 123: 10, 678: 1000, abc: 100 }
```

## 3 VSCode配置

如果每次都要手动注释/反注释`#define __DEBUG__`就太麻烦了，那么我们可以平时注释掉`__DEBUG__`，在VSCode的编译阶段时，我们通过添加编译参数`-D__DEBUG__`来自动添加此宏定义。这样交题的时候LOG无效，在本地运行的时候LOG有效。

### 3.1 task.json

注意args中要添加"-D__DEBUG__"，label要自定义名字，否则launchTask在调用时，会调用默认task，而不是这段task。

```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: Debug",
            "command": "g++.exe",
            "args": [
                "-D__DEBUG__",
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

### 3.2 launch.json

preLaunchTask要修改为刚刚自定义的task label。

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "g++.exe - 生成和调试活动文件",
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}\\${fileBasenameNoExtension}.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": true,
            "MIMode": "gdb",
            "miDebuggerPath": "gdb.exe",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "C/C++: Debug"
        }
    ]
}
```

## 4 一个小技巧

在调试时，除了在VARIABLES里面可以看到当前局部变量，把鼠标放在代码中的变量名上，还可以直接看到该变量的值，而且数组也可以看，非常方便。
