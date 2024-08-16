---
title: MSYS2 安装 mingw-w64-clang 工具链
date: 2022-06-02T16:45:00+08:00
categories: 
- C++
tags:
- MSYS2
- MinGW
- Clang
- LLVM
---

## 1 MSYS2

[MSYS2官网](https://www.msys2.org/)废话不多，上来告诉你啥是`MSYS2`然后教你怎么使用。

> MSYS2 is a collection of tools and libraries providing you with an easy-to-use environment for building, installing and running native Windows software.

机翻：`MSYS2`是一组工具和库，为您提供易于使用的环境，用于构建、安装和运行本机`Windows`软件。

<!-- more -->

去官网下载安装器安装即可。

## 2 使用`MSYS2 MSYS`安装mingw-w64-clang工具链

安装好了以后可以在菜单搜到`MSYS2 MSYS`这个命令行交互软件。

在里面运行以下指令，更新包数据库。第一更新以后软件会自动关闭，需要重新打开软件，再次运行这个命令，完成后续更新。更新过程中由于网络问题也会遇到failed，这时还需要再次运行这个命令，直到没有报错。

``` msys2
pacman -Syu
```

然后安装mingw-w64-clang工具链，安装工具链中所有的内容即可。

```msys2
pacman -S mingw-w64-clang-x86_64-toolchain
```

## 3 设置环境变量

将`安装目录\bin\`放到`PATH`全局变量里面，并重启电脑，这样clang，g++，gdb等命令就比较方便的使用了。

路径eg. `D:\msys64\clang64\bin`
