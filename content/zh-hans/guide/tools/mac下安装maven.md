---
title: mac 下安装 maven
date: 2020-09-26T20:32:05+08:00
categories: 
- 实用工具
tags:
- Maven
- MacOS
---

不知道我当年为啥要这样安maven，brew install不香嘛(2021年12月10日注)

<!-- more -->

## 安装

```shell
cd /usr/local
sudo wget https://mirrors.bfsu.edu.cn/apache/maven/maven-3/3.6.3/binaries/
sudo tar -zxvf apache-maven-3.6.3-bin.tar.gz
sudo rm apache-maven-3.6.3-bin.tar.gz

export MAVEN_HOME=/usr/local/apache-maven-3.6.3
export PATH=${PATH}:${MAVEN_HOME}/bin

source /etc/profile
source ~/.bash_profile

mvn -v
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
Maven home: /usr/local/apache-maven-3.6.3
Java version: 1.8.0_152-release, vendor: JetBrains s.r.o, runtime: /Applications/Android Studio.app/Contents/jre/jdk/Contents/Home/jre
Default locale: zh_CN, platform encoding: UTF-8
OS name: "mac os x", version: "10.15.6", arch: "x86_64", family: "mac"
```

镜像

```xml
<mirrors>
  <mirror>
    <id>alimaven</id>
    <name>aliyun maven</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    <mirrorOf>central</mirrorOf>
  </mirror>
</mirrors>
```