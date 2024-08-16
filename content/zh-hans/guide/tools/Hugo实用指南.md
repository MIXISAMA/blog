---
title: Hugo 实用指南
date: 2022-11-18T02:23:00+08:00
categories: 
- 实用工具
tags:
- Hugo
---

hugo相对hexo的优势逐渐越来大，趁着更换服务器，也顺便把hexo更换为hugo。

<!-- more -->

## 1 安装与创建项目

```bash
sudo snap install hugo
hugo new site <project name>
cd <project name>
git init
# 使用 monochrome 主题
git submodule add https://github.com/kaiiiz/hugo-theme-monochrome.git themes/hugo-theme-monochrome
echo theme = \"hugo-theme-monochrome\" >> config.toml
```

主题列表：<https://themes.gohugo.io/>

站点配置文件 `config.toml`

```toml
# 设置简体中文
languageCode = 'zh-Hans'
```

## 2 使用命令

```bash
# 创建内容
hugo new posts/my-first-post.md
# 开启 hugo 服务
hugo server -D
# 构建静态页面
hugo -D
```

因为我喜欢直接部署在服务器，hugo有个permalink问题，需要在命令行配置修改。

```bash
hugo server --baseUrl=<url> --port=<port> --appendPort=false
```

## 3 文章前题

官方文档：<https://gohugo.io/content-management/front-matter/>

常用

* title: 标题
* categories: 分类
* date: 日期
* description: 描述
* slug: url尾部
* tags: 标签
* draft: 草稿

