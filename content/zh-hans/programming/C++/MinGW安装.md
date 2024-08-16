---
title: MinGW 安装
date: 2020-11-23T16:40:00+08:00
categories: 
- C++
tags:
- MinGW
- C++
- 过时
---

实际上存在两种MinGW，一种就叫MinGW，另一种叫MinGW-W64。两者内容形式上几乎完全相同，实际上却是两个独立的项目。

* MinGW: 仅支持32位编译，官网最新版本2017年更新。
* MinGW-W64: 支持32位和64位编译，GCC官方支持，官网最新版本2020年10月更新。

相比较之下还是比较建议安装MinGW-W64。(跳过1节，看2节)

<!-- more -->

## 1 MinGW

1. [MinGW官网](http://www.mingw.org/)
2. 点击右上角Download
3. 下载`mingw-get-setup.exe`
4. 运行安装

### 1.1 g++安装

1. 运行刚刚安装的`MinGW Installation Manager`
2. 右键`ming32-gcc-g++-bin` -> `Mark for Installation`
3. `Installation` -> `Apply Change` -> `Apply`
  * 此步可能会因为网络问题安装失败。
  * 实在不行选中ming32-gcc-g++-bin，点General，有几个URL把他们下载下来放到安装目录\var\cache\mingw-get\packages\里面，重启软件，再安装。

### 1.2 gdb安装

1. 选择`All Packages` -> `MinGW` -> `MinGW Base System` -> `MinGW Source-Level Debugger`
2. 右键`mingw32-gdb-bin` -> `Mark for Installation`
3. `Installation` -> `Apply Change` -> `Apply`

此步99%因网络问题安装失败，解决办法参考上面g++安装。

### 1.3 make安装

吐槽一句MinGW安装管理器竟然都不提供make，想安装需要输入指令。。。

```shell
cd 安装目录\bin\
mingw-get install mingw32-make
```

## 2 MinGW-W64

1. [MinGW-W64官网](http://mingw-w64.org/)
2. 点击Download
3. 找到SourceForge链接点击进入
4. 下载`MinGW-W64-install.exe`并运行安装

根据下面的解释自行调整安装设置。

* Version: GCC版本, 可以选常用oj的GCC版本，也可以选最新版
* Architecture: 架构
  * i686: 32位系统
  * x86_64: 64位系统
* Threads: 线程模型
  * posix: p线程（建议）
  * win32: win32api
* Exception: 异常处理模型
  * seh: 性能好，不支持32位
  * sjlj: 稳定性好，支持32位

## 3 添加全局变量

将`安装目录\bin\`放到`PATH`全局变量里面，并重启电脑，这样g++，gdb等命令就比较方便的使用了。

路径eg. `D:\mingw-w64\mingw64\bin` 或 `D:\MinGW\bin`

