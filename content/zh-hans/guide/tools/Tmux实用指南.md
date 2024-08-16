---
title: Tmux 实用指南
date: 2022-11-18T02:51:21+08:00
categories: 
- 实用工具
tags:
- Tmux
---

在本人使用 Screen 过程中遇到了很多问题，命令也很难记。Tmux 不仅 bug 少，命令清晰，而且两个人可以同时共享一个 Tmux 会话，这是极其方便的。在 Tmux 基础上还可以安装 Byobu，Byobu 提供了更友好的快捷键。

<!-- more -->

阮一峰老师已经发布过一遍Tmux使用教程的文章：<http://www.ruanyifeng.com/blog/2019/10/tmux.html>

官方仓库wiki：<https://github.com/tmux/tmux/wiki>

```bash
# 安装
sudo apt install tmux
```

```bash
# 进入tmux
tmux
# 退出tmux
exit

# 新建会话
tmux new -s <session-name>
# 分离会话
tmux detach
# 接入会话
tmux attach -t <session-name>
# 杀掉会话
tmux kill-session -t <session-name>
# 切换会话
tmux switch -t <session-name>
# 重命名会话
tmux rename-session -t 0 <new-name>
```

* Ctrl+b d：分离当前会话。
* Ctrl+b s：列出所有会话。
* Ctrl+b $：重命名当前会话。

```bash
# 划分上下两个窗格
$ tmux split-window
# 划分左右两个窗格
$ tmux split-window -h
# 光标切换到上方窗格
$ tmux select-pane -U
# 光标切换到下方窗格
$ tmux select-pane -D
# 光标切换到左边窗格
$ tmux select-pane -L
# 光标切换到右边窗格
$ tmux select-pane -R
# 当前窗格上移
$ tmux swap-pane -U
# 当前窗格下移
$ tmux swap-pane -D
```

* Ctrl+b %：划分左右两个窗格。
* Ctrl+b "：划分上下两个窗格。
* Ctrl+b <arrow key>：光标切换到其他窗格。<arrow key>是指向要切换到的窗格的方向键，比如切换到下方窗格，就按方向键↓。
* Ctrl+b ;：光标切换到上一个窗格。
* Ctrl+b o：光标切换到下一个窗格。
* Ctrl+b {：当前窗格与上一个窗格交换位置。
* Ctrl+b }：当前窗格与下一个窗格交换位置。
* Ctrl+b Ctrl+o：所有窗格向前移动一个位置，第一个窗格变成最后一个窗格。
* Ctrl+b Alt+o：所有窗格向后移动一个位置，最后一个窗格变成第一个窗格。
* Ctrl+b x：关闭当前窗格。
* Ctrl+b !：将当前窗格拆分为一个独立窗口。
* Ctrl+b z：当前窗格全屏显示，再使用一次会变回原来大小。
* Ctrl+b Ctrl+<arrow key>：按箭头方向调整窗格大小。
* Ctrl+b q：显示窗格编号。

