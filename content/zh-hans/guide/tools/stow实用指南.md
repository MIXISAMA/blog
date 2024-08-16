---
title: stow 实用指南
date: 2022-09-05T01:55:48+08:00
categories: 
- 实用工具
tags:
- stow
- Linux
---

这东西两个月不用也忘啊，所以赶紧记一下。

有些库安装以后出现这个问题那个问题，然后因为各种原因卸载不掉。那个感觉就如同去厕所串稀，屎飞到了地上、墙上、天花板上，然后拿个手电筒去找哪里有屎手动清掉。俺主要用stow做个隔离，版本控制这个功能暂时还没用到。那stow具体是啥下面是官网解释我懒得翻译了。

<!-- more -->

>GNU Stow is a symlink farm manager which takes distinct packages of software and/or data located in separate directories on the filesystem, and makes them appear to be installed in the same place. For example, /usr/local/bin could contain symlinks to files within /usr/local/stow/emacs/bin, /usr/local/stow/perl/bin etc., and likewise recursively for any other subdirectories such as .../share, .../man, and so on.
>
>This is particularly useful for keeping track of system-wide and per-user installations of software built from source, but can also facilitate a more controlled approach to management of configuration files in the user's home directory, especially when coupled with version control systems.
>
>Stow is implemented as a combination of a Perl script providing a CLI interface, and a backend Perl module which does most of the work. Stow is Free Software, licensed under the GNU General Public License.

## 1 安装

按理说linux下这样就可以安装stow了

```
apt install stow
```

## 2 使用

stow是把安装好的库链接到`/usr/local/`下面，所以需要先把库安装到一个专门让stow管理的地方，网上大多数人喜欢装到`/usr/local/stow/`里面，我之前也试过装到别的地方，并没有什么影响，但是无论在哪最好都放到一个地方。这里就按网上的大多数人的习惯来。

在configure时应该修改默认安装目录：

```
./configure --prefix=/usr/local/stow/<库名>
```

同理如果是CMake安装应该修改`CMAKE_INSTALL_PREFIX`为`/usr/local/stow/<库名>`

安装好了以后我们就可以用stow来做链接，默认添加链接到`/usr/local/`，命令很简单。

```
cd /usr/local/stow/<库名>
stow <库名>
stow -v <库名> # -v 显示添加链接的详细信息
```

逆向操作收回之前的链接只需添加`-D`参数。

```
cd /usr/local/stow/<库名>
stow -D <库名>
stow -v -D <库名> # -v 显示删除链接的详细信息
```
