---
title: CentOS7.3 各种环境安装
date: 2020-12-08T00:53:27+08:00
categories: 
- Linux
tags:
- CentOS
- Linux
---

本文记录了一些博主平时用到的环境，随着IBM收购Red Hat, CentOS的未来可能被RHEL取代, 博主的服务器系统也更换为Ubuntu20(2021年12月11日注)

<!-- more -->

## Anaconda

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

## git

```shell
yum install git
git --version
git config --global user.name xxx
git config --global user.email xxx@xxx.com
git config --list
```

## zip unzip

```shell
yum install zip unzip
```

## nginx

```shell
yum install nginx
```

## nodejs cnpm

```shell
yum install nodejs
yum update openssl
npm install -g cnpm --registry=https://registry.npm.taobao.org
cnpm install -g n
n latest
PATH="$PATH"
node -v
```

