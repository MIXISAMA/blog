---
title: Linux 常用命令
date: 2020-09-26T20:20:07+08:00
categories: 
- Linux
tags:
- Linux
---

在使用Linux过程中，会经常使用一些系统命令，本文主要记录这些常用命令的用法。

<!-- more -->

## cat

cat（英文全拼：concatenate）命令用于连接文件并打印到标准输出设备上。

cat 的小技巧很多

```shell
# 把 textfile1 的文档内容加上行号后输入 textfile2 这个文档里：
cat -n textfile1 > textfile2

# 把 textfile1 和 textfile2 的文档内容加上行号（空白行不加）之后将内容附加到 textfile3 文档里：
cat -b textfile1 textfile2 >> textfile3

# 清空 /etc/test.txt 文档内容：
cat /dev/null > /etc/test.txt

# cat 也可以用来制作镜像文件。例如要制作软盘的镜像文件，将软盘放好后输入：
cat /dev/fd0 > OUTFILE

# 相反的，如果想把 image file 写到软盘，输入：
cat IMG_FILE > /dev/fd0
```

## more

Linux more 命令类似 cat ，不过会以一页一页的形式显示，更方便使用者逐页阅读，而最基本的指令就是按空白键（space）就往下一页显示，按 b 键就会往回（back）一页显示，而且还有搜寻字串的功能（与 vi 相似），使用中的说明文件，请按 h 。

```shell
# 逐页显示 testfile 文档内容，如有连续两行以上空白行则以一行空白行显示。
more -s testfile

# 从第 20 行开始显示 testfile 之文档内容。
more +20 testfile
```

* Enter 向下n行，需要定义。默认为1行
* Ctrl+F 向下滚动一屏
* 空格键 向下滚动一屏
* Ctrl+B 返回上一屏
* = 输出当前行的行号
* ：f 输出文件名和当前行的行号
* V 调用vi编辑器
* !命令 调用Shell，并执行命令
* q 退出more

# find


