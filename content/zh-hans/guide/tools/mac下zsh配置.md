---
title: mac 下 zsh 配置
date: 2020-09-26T19:32:27+08:00
categories: 
- 实用工具
tags:
- zsh
- MacOS
---

本文修改自：Mac OS 终端 iTerm2 并配置 zsh —— 一蓑烟羽

从 macOS Catalina 版开始，Mac 将使用 zsh 作为默认登录 Shell 和交互式 Shell。

然而 zsh 可以进行配置实现主题、高亮、自动提示等功能。

<!-- more -->

## 1 zsh
Mac 系统自带了zsh, 一般不是最新版，如果需要最新版可通过Homebrew来安装。

```shell
brew install zsh
```

可通过 `zsh –version` 查看zsh的版本。

安装完成以后，将zsh设置为默认的Shell。

```shell
chsh -s /bin/zsh
```

## 2 安装oh-my-zsh

官网给的安装命令被墙了。

```shell
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

这里使用镜像。

```shell
sh -c "$(curl -fsSL https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh)"
```

安装完成。

```shell
Cloning Oh My Zsh...
Cloning into '/Users/zhangjunbo/.oh-my-zsh'...
remote: Enumerating objects: 1158, done.
remote: Counting objects: 100% (1158/1158), done.
remote: Compressing objects: 100% (1127/1127), done.
remote: Total 1158 (delta 20), reused 1061 (delta 15), pack-reused 0
Receiving objects: 100% (1158/1158), 778.28 KiB | 25.00 KiB/s, done.
Resolving deltas: 100% (20/20), done.

Looking for an existing zsh config...
Found ~/.zshrc. Backing up to /Users/zhangjunbo/.zshrc.pre-oh-my-zsh
Using the Oh My Zsh template file and adding it to ~/.zshrc.

         __                                     __
  ____  / /_     ____ ___  __  __   ____  _____/ /_
 / __ \/ __ \   / __ `__ \/ / / /  /_  / / ___/ __ \
/ /_/ / / / /  / / / / / / /_/ /    / /_(__  ) / / /
\____/_/ /_/  /_/ /_/ /_/\__, /    /___/____/_/ /_/
                        /____/                       ....is now installed!


Before you scream Oh My Zsh! please look over the ~/.zshrc file to select plugins, themes, and options.

• Follow us on Twitter: https://twitter.com/ohmyzsh
• Join our Discord server: https://discord.gg/ohmyzsh
• Get stickers, shirts, coffee mugs and other swag: https://shop.planetargon.com/collections/oh-my-zsh
```

安装完成以后，默认Shell的/.bashrc文件默认不再加载了，替代的是/.zlogin和/.zshrc。所以如果你在/.bashrc里配置了某些设置，需要把她们复制到~/.zshrc中。

### oh my zsh 目录结构

进入~/.oh-my-zsh目录后，看看该目录的结构

```shell
ls ~/.oh-my-zsh
CONTRIBUTING.md cache           log             templates
LICENSE.txt     custom          oh-my-zsh.sh    themes
README.md       lib             plugins         tools
```

* lib 提供了核心功能的脚本库
* tools 提供安装、升级等功能的快捷工具
* plugins 自带插件的存在放位置
* templates 自带模板的存在放位置
* themes 自带主题文件的存在放位置
* custom 个性化配置目录，自安装的插件和主题可放这里

## 3 配置zsh

zsh 的配置主要集中在~/.zshrc里，用 vim 或你喜欢的其他编辑器打开.zshrc。

可以在此处定义自己的环境变量和别名，当然，oh my zsh 在安装时已经自动读取当前的环境变量并进行了设置，你可以继续追加其他环境变量。

### 3.1 别名设置

zsh不仅可以设置通用别名，还能针对文件类型设置对应的打开程序，比如：

* alias -s html=vi，意思就是你在命令行输入 hello.html，zsh会为你自动打开vim并读取hello.html；
* alias -s gz='tar -xzvf'，表示自动解压后缀为gz的压缩包。

```shell
alias cls='clear'
alias ll='ls -l'
alias la='ls -a'
alias vi='vim'
alias javac="javac -J-Dfile.encoding=utf8"
alias grep="grep --color=auto"
alias -s html=code
alias -s rb=code
alias -s py=code
alias -s js=code
alias -s c=code
alias -s java=vi
alias -s txt=code
alias -s gz='tar -xzvf'
alias -s tgz='tar -xzvf'
alias -s zip='unzip'
alias -s bz2='tar -xjvf'
```

### 3.2 主题设置

oh my zsh 提供了数十种主题，相关文件在`~/.oh-my-zsh/themes`目录下，你可以自己选择，也可以自己编写主题。

在.zshrc里找到ZSH_THEME，就可以设置主题了，默认主题是`：ZSH_THEME=”robbyrussell”`

`ZSH_THEME="random"`，主题设置为随机，这样我们每打开一个窗口，都会随机在默认主题中选择一个。

### 3.3 插件设置

oh my zsh项目提供了完善的插件体系，相关的文件在~/.oh-my-zsh/plugins目录下，默认提供了100多种，大家可以根据自己的实际学习和工作环境采用，想了解每个插件的功能，只要打开相关目录下的 zsh 文件看一下就知道了。插件也是在.zshrc里配置，找到plugins关键字，你就可以加载自己的插件了，系统默认加载git，你可以在后面追加内容，如下：

```shell
plugins=(git zsh-autosuggestions autojump zsh-syntax-highlighting)
```

#### 3.3.1 安装 zsh-autosuggestions

```shell
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

添加至 plugins

#### 3.3.2 安装 zsh-syntax-highlighting

```shell
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

添加至 plugins

## 4 卸载oh my zsh

直接在终端中，运行`uninstall_oh_my_zsh`即可以卸载。

