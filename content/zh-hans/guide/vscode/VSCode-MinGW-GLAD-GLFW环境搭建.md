---
title: VSCode + MinGW + GLAD + GLFW 环境搭建
date: 2020-12-08T01:08:01+08:00
categories: 
- VSCode
tags:
- VSCode
- OpenGL
---

## 1 MinGW

本文使用MinGW-W64，安装参考VSCode+MinGW环境搭建。这里使用另外的配置文件，MinGW环境变量配好即可。

<!-- more -->

## 2 GLFW下载

[GLFW官网](https://www.glfw.org/)，右上角进入Download页面。

我的MinGW是64位版本，所以要下载对应的64位预编译版本。下载好解压到一个地方妥善保管。

## 3 GLAD下载

[GLAD2下载](https://gen.glad.sh/)

如下配置选项

* Language: C/C++
* APIs-gl: Version4.6(最新版) Compatibility
* Options: debug loader

点击GENERATE，下载glad.zip，解压到一个地方妥善保管。

## 4 VSCode工程

新建一个目录作为工程目录，并用VSCode打开。在工程根目录下新建五个文件夹。

* .vscode: VSCode配置文件夹
* Debug: dll文件和编译产生的exe所在文件夹
* include: .h文件所在文件夹
* lib: .a文件所在文件夹
* src: .c/.cpp文件所在文件夹

接下来是在工程目录中放置glad和glfw的sdk。

* 将之前下载的`glad/include`下的`glad`文件夹和`KHR`文件夹，放入项目的`include`文件夹中。
* 将之前下载的`glad/src`下的`gl.c`文件，放入项目的`src`文件夹中。
* 将之前下载的`glfw/include`下的GLFW文件夹，放入项目的`include`文件夹中。
* 将之前下载的`glfw/lib-mingw-w64`下的`libglfw3.a`和`libglfw3dll.a`文件，放入项目的`lib`文件夹中。
* 将之前下载的`glfw/lib-mingw-w64`下的`glfw3.dll`文件，放入项目的`Debug`文件夹中。

因为`gl.c`文件是以源码形式给出的，我们可以将其编译为静态链接库。

```shell
g++ -c src/gl.c -I include
ar -rc lib/libgl.a gl.o
del gl.o
```

## 5 配置VSCode

其实现在通过适当的编译命令，就已经可以编写和运行我们的第一个opengl程序了，但是这个命令会很长，我们可以通过配置VSCode的方式来自动帮我们添加这些编译参数。

### 5.1 settings.json

在`.vscode`中创建`settings.json`，里面这样写。

```json
{
    "C_Cpp.default.cppStandard": "c++14",
    "files.encoding": "gbk",
    "C_Cpp.default.includePath": [
        "${workspaceFolder}/include/**",
        "%MinGWInclude%/**",
    ],
}
```

### 5.2 tasks.json

在`.vscode`中创建`tasks.json`，里面这样写。

```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: Debug",
            "command": "g++.exe",
            "args": [
                "-g",
                "${file}",
                "-o",
                "${workspaceFolder}/DeBug/${fileBasenameNoExtension}.exe",
                "-I",
                "${workspaceFolder}/include/",
                "-L",
                "${workspaceFolder}/lib/",
                "-lopengl32",
                "-lgl",
                "${workspaceFolder}/Debug/glfw3.dll"
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

### 5.3 launch.json

在.vscode中创建launch.json，里面这样写。

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
            "program": "${workspaceFolder}/Debug/${fileBasenameNoExtension}.exe",
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

## 6 试试官方实例

下载`boing.c`文件到`src`文件夹里。

下载`boing.c`的依赖`linmath.h`文件到`include`文件夹里面。

然后打开`boing.c`按F5调试运行。调试窗口一直在报错`GLAD: ERROR 1282 in glEnd!`说明里面还是有bug滴，等待你的pr（参与知名项目的开发）。
