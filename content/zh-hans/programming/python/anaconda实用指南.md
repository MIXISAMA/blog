---
title: Anaconda 实用指南
date: 2020-12-03T00:33:26+08:00
categories: 
- Python
tags: 
- anaconda
- Python
---

本文介绍了anaconda的常用命令，以及Linux手动安装Anaconda的方法。

<!-- more -->

## 常用命令

```shell
# 创建新环境
conda create --name <env_name> <package_names>
conda create --name a_py_env python=3.9
# 切换环境
activate <env_name>
# 删除环境
conda remove --name <env_name> --all
# 列出所有环境
conda env list
```

## Linux手动安装Anaconda

```shell
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2020.11-Linux-x86_64.sh
bash Anaconda3-2020.11-Linux-x86_64.sh

...
[/root/anaconda3] >>> /usr/local/anaconda3
...
init >>> yes

source ~/.bash_profile
rm Anaconda3-2020.11-Linux-x86_64.sh
```
