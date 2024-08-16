---
title: git submodule 使用指南
date: 2022-01-08T01:58:14+08:00
categories: 
- git
tags:
- git
---

## 添加子模块

以DbConnector仓库为例

```shell
git submodule add https://github.com/chaconinc/DbConnector
```

默认子模块添加在项目根目录，文件夹名为子模块名，可以在后面添加路径改变子模块存放位置。

```shell
git submodule add https://github.com/chaconinc/DbConnector 3rdparty/DbConnector
```

使用`git checkout`使用确定版本的子模块。

```shell
cd DbConnector/
git checkout stable
```

## 克隆含有子模块的项目

```shell
git clone https://github.com/chaconinc/MainProject
git submodule init
git submodule update
```

或者

```shell
git clone --recurse-submodules https://github.com/chaconinc/MainProject
```

## 删除子模块

这里复制的其他人的博客，亲测完美。

有时子模块的项目维护地址发生了变化，或者需要替换子模块，就需要删除原有的子模块。

删除子模块较复杂，步骤如下：

```shell
rm -rf # 子模块目录 删除子模块目录及源码
vi .gitmodules # 删除项目目录下.gitmodules文件中子模块相关条目
vi .git/config # 删除配置项中子模块相关条目
rm .git/module/* # 删除模块下的子模块目录，每个子模块对应一个目录，注意只删除对应的子模块目录即可
```

执行完成后，再执行添加子模块命令即可，如果仍然报错，执行如下：

```shell
git rm --cached 子模块名称
```

完成删除后，提交到仓库即可。
