---
title: mac 下安装 Hadoop3
date: 2020-09-26T19:48:49+08:00
categories: 
- 实用工具
tags:
- Hadoop
- MacOS
---

之前(2019年左右)有前辈说windows很难配置Hadoop环境，对于学习Hadoop来说，MacOS是很好用的系统，在配置环境这一块可以少踩坑。

<!-- more -->

## 1 创建hadoop用户

依照论坛上的人说hadoop的安全性做的不好，不应该使用root/管理员用户来使用hadoop，所以我们创建一个hadoop用户。

```shell
sudo dscl . -create /Users/hadoop UserShell /bin/bash
sudo dscl . -create /Users/hadoop UniqueID 601
sudo dscl . -create /Users/hadoop RealName hadoop
sudo dscl . -passwd /Users/hadoop 123456
sudo dscl . -create /Groups/hadoop
sudo dscl . -append /Groups/hadoop GroupMembership hadoop
```

## 2 安装依赖

hadoop依赖Java和ssh、pdsh，电脑里已经有Java和ssh了，安装pdsh就好。

```
brew install pdsh
```

## 3 下载解压

下载镜像

```shell
cd ~
wget https://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/hadoop-3.2.1/hadoop-3.2.1.tar.gz
```

解压

```shell
tar -xzvf hadoop-3.2.1.tar.gz
```

测试

```shell
hadoop-3.2.1/bin/hadoop version
```

## 4 设置JAVA_HOME路径

修改`etc/hadoop/hadoop_env.sh`，添加JAVA_HOME

```sh
# The java implementation to use. By default, this environment
# variable is REQUIRED on ALL platforms except OS X!
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_112.jdk/Contents/Home
```

## 5 修改用户名

修改`etc/hadoop/hadoop_env.sh`

```sh
export HDFS_DATANODE_SECURE_USER=zhangjunbo
export HDFS_SECONDARYNAMENODE_USER=zhangjunbo
export HDFS_NAMENODE_USER=zhangjunbo
```

修改`bin/hdfs`

```sh
HADOOP_SHELL_EXECNAME="zhangjunbo"
```

## 6 配置ssh

系统便好设置->共享->☑远程登录->仅这些用户

配置本机用户免密登陆

```shell
cd ~/.ssh/                     # 若没有该目录，请先执行一次ssh localhost
ssh-keygen -t rsa              # 会有提示，都按回车就可以
cat ./id_rsa.pub >> ./authorized_keys  # 加入授权
```

这个地方要保证`authorized_keys`只有这一个公钥，不要发生歧义。

## 7 伪分布式配置

修改`etc/hadoop/core-site.xml`。

* hadoop.tmp.dir 是tmp文件的位置，若不设置会被放置在系统默认文件夹中，容易被系统清理。
* fs.defaultFS 是文件系统访问路径。

```xml
<configuration>
  <property>
    <name>hadoop.tmp.dir</name>
    <value>file:/Users/zhangjunbo/hadoop-3.2.1/tmp</value>
    <description>Abase for other temporary directories.</description>
  </property>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
  </property>
</configuration>
```

修改配置文件`hdfs-site.xml`。

* dfs.replicateion 是冗余备份的数量
* 另外两个是namenode和datanode存放的位置。

```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>file:/Users/zhangjunbo/hadoop-3.2.1/tmp/dfs/name</value>
  </property>
  <property>
    <name>dfs.namenode.data.dir</name>
    <value>file:/Users/zhangjunbo/hadoop-3.2.1/tmp/dfs/data</value>
  </property>
</configuration>
```

## 8 Hadoop配置文件说明

Hadoop 的运行方式是由配置文件决定的（运行 Hadoop 时会读取配置文件），因此如果需要从伪分布式模式切换回非分布式模式，需要删除 core-site.xml 中的配置项。

此外，伪分布式虽然只需要配置 fs.defaultFS 和 dfs.replication 就可以运行（官方教程如此），不过若没有配置 hadoop.tmp.dir 参数，则默认使用的临时目录为 /tmp/hadoo-hadoop，而这个目录在重启时有可能被系统清理掉，导致必须重新执行 format 才行。所以我们进行了设置，同时也指定 dfs.namenode.name.dir 和 dfs.datanode.data.dir，否则在接下来的步骤中可能会出错。

## 9 namenode格式化

配置完成后，执行 NameNode 的格式化:

```shell
cd hadoop-hadoop-3.2.1
bin/hdfs namenode -format
```

执行结果

```txt
2020-09-27 14:41:58,596 INFO common.Storage: Storage directory /tmp/hadoop-root/dfs/name has been successfully formatted.
2020-09-27 14:41:58,718 INFO namenode.FSImageFormatProtobuf: Saving image file /tmp/hadoop-root/dfs/name/current/fsimage.ckpt_0000000000000000000 using no compression
2020-09-27 14:41:58,796 INFO namenode.FSImageFormatProtobuf: Image file /tmp/hadoop-root/dfs/name/current/fsimage.ckpt_0000000000000000000 of size 399 bytes saved in 0 seconds .
2020-09-27 14:41:58,806 INFO namenode.NNStorageRetentionManager: Going to retain 1 images with txid >= 0
2020-09-27 14:41:58,810 INFO namenode.FSImage: FSImageSaver clean checkpoint: txid=0 when meet shutdown.
2020-09-27 14:41:58,810 INFO namenode.NameNode: SHUTDOWN_MSG:
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at localhost/127.0.0.1
************************************************************/
```

## 10 开启 NameNode 和 DataNode 守护进程

```shell
sbin/start-dfs.sh
```

>启动时可能会出现如下 WARN 提示：WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform… using builtin-java classes where applicable WARN 提示可以忽略，并不会影响正常使用。

启动完成后，可以通过命令 jps 来判断是否成功启动，若成功启动则会列出如下进程: “NameNode”、”DataNode” 和 “SecondaryNameNode”（如果 SecondaryNameNode 没有启动，请运行 sbin/stop-dfs.sh 关闭进程，然后再次尝试启动尝试）。如果没有 NameNode 或 DataNode ，那就是配置不成功，请仔细检查之前步骤，或通过查看启动日志排查原因。

```txt
13410 SecondaryNameNode
13171 NameNode
13274 DataNode
13535 Jps
```

成功启动后，可以访问 Web 界面 http://localhost:9870 查看 NameNode 和 Datanode 信息，还可以在线查看 HDFS 中的文件。

