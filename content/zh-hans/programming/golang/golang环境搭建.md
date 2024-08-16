---
title: golang 环境搭建
date: 2022-04-16T14:58:00+08:00
categories: 
- golang
tags:
- golang
- VSCode
---

golang简单高效的并发深得朕心，不过对于内存的操作，我觉得C++更胜一筹。

<!-- more -->

## 1. 安装

### 1.1 Linux

```shell
# 下载
wget https://go.dev/dl/go1.18.1.linux-amd64.tar.gz
# 删掉原来的
sudo rm -rf /usr/local/go
# 直接解压到/usr/local/
sudo tar -C /usr/local -xzf go1.18.1.linux-amd64.tar.gz
# 手动添加到环境变量
export PATH=$PATH:/usr/local/go/bin
```

### 1.2 MacOS & Windows

<https://go.dev/dl/>

## 2. 常用指令

<https://go.dev/ref/mod#mod-commands>

### 2.1 `go init`

```shell
go mod init github.com/MIXISAMA/gobang/backend
```

`go mod init`用来初始化一个新的golang项目。

### 2.2 `go mod tidy`

```shell
go mod tidy
```

`go mod tidy`用来同步`go.mod`文件与源码中使用的依赖模块相匹配。


### 2.3 `go get`

```shell
# golang 1.18版本以后弃用-d，并且默认开启-d
# -d 要求get后不构建和安装golang包，推荐1.18以前全部使用-d
# -t 要求get后构建测试包
# -u 要求更新到最新版本
go get [-d] [-t] [-u] [build flags] [packages]

go get -d golang.org/x/net
go get -d golang.org/x/text@v0.3.2
# 升级当前模块中所有的依赖包
go get -d -u ./...
```

### 2.4 `go install`

```shell
go install golang.org/x/tools/gopls@latest
```

### 2.5 `go list -m`

```shell
go list -m all
go list -m -versions example.com/m
go list -m -json example.com/m@latest
```

### 2.6 `go run`

```shell
go run .
go run [go-project-name]
```

### 2.7 Module-aware commands 感知命令

下面是所有的感知命令，相当于既可以对当前项目使用，也可以对GOPATH中的全局项目使用。

* go build
* go fix
* go generate
* go get
* go install
* go list
* go run
* go test
* go vet

## 3 代理

```shell
# 配置 GOPROXY 环境变量
export GOPROXY=https://proxy.golang.com.cn,direct
# 还可以设置不走 proxy 的私有仓库或组，多个用逗号相隔（可选）
export GOPRIVATE=git.mycompany.com,github.com/my/private
```

## 4 在VSCode中编写golang项目

1. 安装Go插件
2. 可能由于网络原因一些go依赖包无法下载，设置代理后重新安装或手动安装VSCode提示的依赖包即可。
