---
title: "Certbot 实用指南"
date: 2022-11-18T03:31:36+08:00
# draft: true
---

Certbot官网: <https://certbot.eff.org/>

建议按照官网指导安装。

<!-- more -->

Nginx + Ubuntu20 使用过程记录如下

```bash
sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
# 生成证书
sudo certbot --nginx
# 自动更新证书
sudo certbot renew --dry-run
```

certbot申请泛域名证书需要用dns插件，有点麻烦以后需要了再弄。

添加新域名: 如果已经有zhangjunbo.com证书, 想要添加www.zhangjunbo.com, 使用以下命令。逗号分隔，中间不能有空格。

```bash
sudo certbot --expand -d zhangjunbo.com,www.zhangjunbo.com
```
