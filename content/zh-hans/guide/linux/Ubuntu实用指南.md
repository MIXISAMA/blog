---
title: Ubuntu 实用指南
date: 2023-06-22T16:15:00+08:00
categories: 
- Linux
tags:
- Ubuntu
- deb
- dpkg
---

### 1 安装 deb 包

`.deb`文件是Debian package的缩写。

```shell
dpkg -i *.deb # 安装包
```

### 2 开启 mdns

```shell
sudo vim /etc/systemd/resolved.conf
MulticastDNS=yes
LLMNR=yes
```

然后重启

### 3 开启 ssh 服务 (scp)

```shell
sudo apt install openssh-server
```

### 4 ifconfig

```shell
sudo apt install net-tools
```

