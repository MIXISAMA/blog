---
title: 一种解决C++循环包含的方法
date: 2023-06-29T22:49:00+08:00
categories: 
- C++
tags:
- C++
---


类A使用类B的一些功能，同时类B使用类A的一些功能

比如Database与Table

```cpp
Table::Table(const Database& db);
Table Database::create_table();
```

Table在构造的时候实际上只需要Database的部分属性(比如句柄，总之不是全部属性)，而create_table作为工厂方法是一定要用到Table的构造方法的。所以可以把Databse中用来构造Table的属性以新类组合或父类继承的形式提取出来，放到另外一个文件。

```cpp
// 组合
Table::Table(const DatabaseHandle& dbhd);
Table(database.handle());

// 继承
Table::Table(const AbstractDatabase& db);
Table(database);
```

第二种方法：前向声明 Forward Declaration

这种目前在GCC上试过从GCC4到GCC9都没有warning，不过在编译的时候，其他Error会引发note这个前向声明，无伤大雅，别的编译器还没试过。

这种在声明定义分离的时候几乎所有情况的都能解决

```cpp
// xxx.h
class A; // 前向声明

class B {
public:
    A CreateA();
}

class A {
public:
    B CreateB();
}
```


