---
title: nginx 实用指南
date: 2021-12-11T15:45:57+08:00
categories: 
- 实用工具
tags:
- nginx
---

本科几年展示的项目都是在调试模式下运行，一直都没有踏入生产环境运维，感觉这些项目都不完整。所以从现在开始多多使用nginx，养成一个开发完整项目的习惯。本文记录一些博主平时用到的nginx命令与配置。

<!-- more -->

## 1 安装

这里用的系统是ubuntu20

```shell
apt install nginx
```

安装完成后nginx会自动开在80端口，直接访问就会有显示。

## 2 常用命令

```shell
nginx -s reload # 重载
nginx -t # 测试配置文件是否正确
```

## 3 配置

### 3.1 根据子域名转发到不同端口

用本博客举例子, 添加配置文件`/etc/nginx/conf.d/blog.conf`, 名字符合`*.conf`格式即可被加载

```conf
server{
  listen 80;
  server_name  blog.zhangjunbo.top;

  location / {
    proxy_pass  http://127.0.0.1:6001;
    proxy_set_header Host $proxy_host; # 转发使用原来的报头
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```

该配置文件实现了`blog.zhangjunbo.top`到`6001端口`的转发

然后重载`nginx -s reload`

但是在https上，用以上配置就会出问题，下面的可以。

```conf
server{
    listen 443 ssl;
    server_name zaobot.zhangjunbo.top;

    ssl_certificate /etc/letsencrypt/live/zhangjunbo.top/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/zhangjunbo.top/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    location / {
        proxy_pass  http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

nginx 默认只能上传2M的文件，可以修改这个上限。

```conf
server{
    client_max_body_size 50M;
    client_body_buffer_size 2M;
}
```