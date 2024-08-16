---
title: MinIO 实用指南
date: 2022-11-20T12:18:00+08:00
categories: 
- 实用工具
tags: 
- MinIO
- PicGo
---

开发过程中，尤其是前端开发中，一定会用到一些静态资源，最简单的方法是放到项目的 static 或是 assets 文件夹里面。但是如果用 git 管理代码以后会出现很大的问题，静态资源一般很多很大又容易频繁更换，这些静态资源相比代码占据了绝大部分仓库空间，这很不环保。所以在开发过程中，可以使用对象存储服务把这些静态资源存放到另外的服务器上。

之前我用过市面上的一些对象存储服务，七牛云每月有一些免费流量，足够个人开发使用，腾讯云免费试用结束就得充钱了。综合考虑后，我决定使用开源的对象存储服务，并且部署在自己的服务器上。MinIO 作为一个开源高性能对象存储项目，在 github 上有几万颗星，所以我就直接拿来用了。

MinIO 仓库地址: <https://github.com/minio/minio>

## 1 安装 MinIO

```bash
wget https://dl.min.io/server/minio/release/linux-amd64/minio
chmod +x minio
```

## 2 运行 MinIO

目前还没找到配置文件方式启动 MinIO 的方法，所以现在使用环境变量的方式添加启动参数。MinIO 有两个对外开发的端口，一个是可视化控制台界面，一个是 API 交互接口，如果使用代理转发，要设置转发的server url。

```bash
MINIO_CONSOLE_ADDRESS="127.0.0.1:9090" \
MINIO_ADDRESS="127.0.0.1:9000" \
MINIO_BROWSER_REDIRECT_URL="https://minio.zhangjunbo.top/" \
MINIO_SERVER_URL="https://api.minio.zhangjunbo.top/" \
MINIO_ROOT_USER=<用户名> \
MINIO_ROOT_PASSWORD=<密码> \
./minio server <数据存储路径>
```

## 3 配置 Bucket

主要是创建 Access Key 和配置存储桶的访问策略。创建 Access Key 很简单不说了。这里说一下如何配置一个只读访问策略。

在存储桶管理页面，找到`Access Rules`创建一个只读规则，我这里前缀设置为`picgo`，然后在`Summary`的`Access Policy`那里设置为`custom`。

## 4 配置 PicGo

PicGo 是一个方便的跨平台的能够上传文件到对象存储服务的客户端。

PicGo 仓库: <https://github.com/Molunerfinn/PicGo>

首先在插件市场里面找 MinIO 插件并安装。我的配置如下。

* endPoint: api.minio.zhangjunbo.top
* port: 443
* useSSL: yes
* accessKey: *access key*
* secretKey: *secret key*
* bucket: *bucket*
* 基础目录: picgo
* 自动归档: yes
