---
title: git 迁移分支到另外一个仓库
date: 2022-11-19T02:46:00+08:00
categories: 
- git
tags:
- git
---

之前换了框架重新实现了计协群里的zaobot，运行了1年多没什么问题。现在要把自己仓库的分支迁移到计协仓库的分支，这里记录一下git指令。

有两种方法开一个新分支，一种基于一个commit，一种创建一个空白分支。

```bash
cd <目的仓库>
# 创建一个空白分支
git checkout --orphan <目的分支>
```

```bash
cd <目的仓库>
# 基于一个commit，开新分支
git checkout <想开分支commit结点>
git checkout -b <目的分支>
```

之后可以对当前项目做出一些更改然后push上去

```bash
cd <目的仓库>
# 删除所有的东西
rm -rf `ls -a | egrep -v '(.|..|.git)'`
git add .
git commit -m "prepare for new brench"
git push --set-upstream origin <目的分支>
```

现在把目的仓库先 pull 到源仓库，然后才能把源仓库 push 到目的仓库上去。

```bash
cd <源仓库>
# 设置目的仓库 remote
git remote add <目的仓库origin名> <目的仓库url>
# 查看当前的 remote
git remote
# 先 pull, 可能要解决一下冲突
git pull <目的仓库origin名> <目的分支> --allow-unrelated-histories
# 再 push
git push <目的仓库origin名> <源分支>:<目的分支>
```

这时源本地仓库会领先源远程仓库一个 merge 版本，我这里选择不 push，并且把源远程仓库 archive，不再使用。

可以更新一下目的本地仓库

```bash
cd <目的仓库>
git pull
```

对于目的仓库有一些分支名字需要修改，用以下命令即可。注意，平台规定默认分支名字无法被修改，只能先去平台更换一下默认分支，然后再修改。

```bash
cd <目的仓库>
git branch -m <目的仓库旧分支名> <目的仓库新分支名>
git push --delete origin <目的仓库旧分支名>
git push origin <目的仓库新分支名>:<目的仓库新分支名>
```
