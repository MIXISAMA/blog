---
title: Linux 下源码编译安装 Python
date: 2022-12-31T22:00:00+08:00
categories: 
- Linux
tags:
- Linux
- Python
---


* 系统环境: Ubuntu 22

刚开始系统里连编译器都没有，所以还要安装gcc编译器。

```bash
sudo apt update
sudo apt install build-essential
```

下载并解压。

```bash
wget https://www.python.org/ftp/python/3.8.0/Python-3.8.16.tgz
tar -xf Python-3.8.16.tgz
```

编译安装。

```bash
cd Python-3.8.16
./configure --enable-optimizations
# ./configure --enable-optimizations --prefix=/home/xxx/python/python-3.8.16
make -j `nproc`
sudo make install
```

最近各个平台又安装了一遍python（2023年6月20日），过来感叹一下。
