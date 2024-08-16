---
title: Hexo 实用指南
date: 2021-12-10T20:20:07+08:00
categories: 
- 实用工具
tags:
- Hexo
- NexT
---

本文介绍了Hexo+Next的使用方法与配置项

<!-- more -->

## 1 安装与创建项目

```shell
npm install hexo
hexo init <folder>
cd <folder>
npm install

# next主题
git clone https://github.com/next-theme/hexo-theme-next themes/next
```

注意不要clone这个`https://github.com/theme-next/hexo-theme-next`, 这个是原本的next仓库, 但是owner人不见了, 所以next团队的人又开了一个新仓库, 现在在新的仓库更新维护.


## 2 命令

```shell

# 创建新md文件
hexo new "My New Post"

# 清理缓存文件
hexo clean

# 生成静态站点
hexo generate

# 在端口6001运行
hexo server -p 6001

# 部署到远程仓库
hexo deploy

```

## 3 next主题

添加tags页面

```shell
hexo new page "tags"
```

修改`source/tags/index.md`

```markdown
---
title: tags
type: tags
---
```

同理添加categories页面

```shell
hexo new page "categories"
```

修改`source/categories/index.md`

```markdown
---
title: categories
type: categories
---
```

## 4 配置

[hexo文档](https://hexo.io/zh-cn/docs/configuration)已经讲的很详细了，这里只写本博客用的到的配置。

### 4.1 `_config.yml`

```yml
# 主题
theme: next
```

### 4.2 `themes/next/_config.yml`

```yml
# 关闭目录标题序号
toc:
  number: false

# 开启菜单
menu:
  home: / || fa fa-home
  tags: /tags/ || fa fa-tags
  categories: /categories/ || fa fa-th
  archives: /archives/ || fa fa-archive
  ...
```
