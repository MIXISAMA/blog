---
title: java + hadoop 最小案例
date: 2020-09-29T20:39:42+08:00
categories: 
- 分布式
tags:
- Java
- Hadoop
- Maven
---

hadoop在后台运行时，可以通过Shell或调用Jar包等方式对hdfs进行操作。本文为eclipse配置maven，使用maven作为hadoop等包的管理工具。然后尝试写一个简单的程序。

<!-- more -->

## 安装maven

~~参考我之前的文章mac下安装maven~~

```shell
brew install maven
```

## eclipse配置maven

本人使用的eclipse为2020-3版本，据说旧版本eclipse对maven的支持不太好，不如myeclipse和idea。

打开eclipse，找到Preferences->Maven->User Settings->Global Settings，填写maven的settings.xml位置路径，如/usr/local/apache-maven-3.6.3/conf/settings.xml，然后点击Update Settings，Apply and Close应用并关闭。

## 新建maven项目

在eclipse，找到`File->New->Project...->Maven->Maven Project`，勾选`Create a simple project`点击`Next`。

* Group id: 工作组名 如 top.zhangjunbo
* Artifact id: 项目名 如 Hadoop Learn
* Packaging
  * jar: 客户端项目 本文使用这个
  * war: 网页项目

## 配置pom.xml

POM是项目对象模型(Project Object Model)的简称，它是Maven项目中的文件，使用XML表示，名称叫做pom.xml。其实通俗来讲就是配置好这个文件保存以后，eclipse会自动将依赖包下载到lib里面。

把下面的配置加到`<project></project>`里面。该配置为hadoop common 3.2.1版本的配置，其他版本或者其他依赖包可以去[mvn仓库](https://mvnrepository.com/)查询。

```xml
<dependencies>
    <!-- https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-common -->
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>3.2.1</version>
    </dependency>
</dependencies>
```

## 配置log4j

在`src/main/resources`里面新建`log4j.properties`文件。填写下面的配置。

```properties
log4j.rootLogger=DEBUG, CA

log4j.appender.CA=org.apache.log4j.ConsoleAppender

log4j.appender.CA.layout=org.apache.log4j.PatternLayout
log4j.appender.CA.layout.ConversionPattern=%-4r [%t] %-5p %c %x - %m%n
```

配置好以后，运行hapdoop程序会自动在控制台打log，方便debug。不配置的话会报Warn。

## 运行代码

```java
package chapter;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;

public class Chapter3 {

    public static void main(String[] args) {
        try {
            String filename = "hdfs://localhost:9000/Users/zhangjunbo/test.txt";
            Configuration conf = new Configuration();
            FileSystem fs = FileSystem.get(conf);
            if(fs.exists(new Path(filename))) {
                System.out.println("文件存在");
            }else {
                System.out.println("文件不存在");
            }
        }catch(Exception e) {
            //System.out.println("fuck");
            e.printStackTrace();
        }
    }
}
```
