---
title: screen 实用指南
date: 2021-12-13T12:36:48+08:00
categories: 
- 实用工具
tags:
- screen
- Linux
---

screen的指令真的很难记，误操作以后又需要用更多其他的指令补救，这里记录一些常用的指令

<!-- more -->

```shell
screen -ls # 列出所有的screen
screen -S <screen name> # 创建一个新的screen
screen -r <screen name> # 切换到这个screen
screen -d <screen name> # 脱离screen
screen -S <screen name> -X quit # 删除此screen
```

在screen中

* `ctrl`+`A`,`D`: 离开screen
