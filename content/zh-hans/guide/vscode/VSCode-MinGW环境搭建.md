---
title: VSCode + MinGW 环境搭建
date: 2020-11-23T20:53:42+08:00
categories: 
- VSCode
tags:
- VSCode
- MinGW
---

## 1 简述

vscode作为代码编辑器，其插件要远远比codeblocks和devc++多，无论是写大工程还单文件小代码都会比他俩舒服的多。网上大多对vscode的配置是使用code runner插件，避开debug功能而不谈。其实debug也是很重要的一个功能，尤其是像c/c++，java这种需要提前编译的语言。对已知缺陷复现时，可以结合Log并使用debug步步跟踪，修复缺陷的效率会比纯Log高的多。

本文是针对G++11单文件可调试的Windows+VSCode+MinGW环境搭建。

<!-- more -->

## 2 VSCode安装

[VSCode官网](https://code.visualstudio.com/)

## 3 编译器安装

（2022年6月2日更新）

~~本人已将该章的过时内容归档至[MinGW安装](https://mixisama.gitee.io/2020/11/23/C++/MinGW安装/)。~~

目前流行使用`clang`+`mingw-w64`作为编译器，安装方法请参考我的另一篇博客：[MSYS2安装mingw-w64-clang工具链](https://mixisama.gitee.io/2022/06/02/C++/MSYS2安装mingw-w64-clang工具链/)。

## 4 配置VSCode

这里全部使用Workspace配置，开了新项目记得把配置重新复制进去，一般我个人刷题，代码都在一个项目里面。

### 4.1 必装插件

插件名就叫`C/C++`，当你创建了cpp文件vscode也会提醒你让你装。

### 4.2 tasks.json

在工程目录的`.vscode`目录（没有自己创建）里面创建`tasks.json`文件，里面这样写。

```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: g++.exe build active file",
            "command": "g++.exe",
            "args": [
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

### 4.3 launch.json

在工程目录的`.vscode`目录（没有自己创建）里面创建`launch.json`文件，里面这样写。

```json
{
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
            "preLaunchTask": "C/C++: g++.exe build active file"
        }
    ]
}
```

### 4.4 settings.json

在工程目录的`.vscode`目录（没有自己创建）里面创建`settings.json`文件，里面这样写。

```json
{
    "C_Cpp.default.cppStandard": "c++11",
    "files.encoding": "gbk"
}
```

## 5 使用

写好一个cpp文件后按F5编译调试运行，建议再main函数return 0处加个断点，防止程序立即退出看不到结果。
