---
title: STL 容器分析总结
date: 2021-12-13T12:53:51+08:00
categories: 
- C++
tags:
- stl
- C++
---

之前面试的时候有被问到`deque`，当时我还觉得这种东西不常用，不屑一顾，直到我在做日志ui的时候，发现这个东西比`list`、`vector`、`queue`更合适做日志存储容器，所以我把我用过的各种stl容器分析总结到本文。

<!-- more -->

## 1 Sequence 内存连续型

### 1.1 array(C++11)

...

### 1.2 vector

...

vector 追加缓存

### 1.3 deque

看到`deque`的名字我还以为是`queue`的扩展，实际上他们两个完全不一样，`deque`的用法更靠近`vector`，比如可以使用`[]`运算符以`O(1)`的时间复杂度随机访问元素，但在添加删除双端元素时，开销比`vector`小。

`deque`允许在双端快速插入删除，并且在双端快速插入删除后，指向其余元素的指针不变。

`deque`和`vector`不同之处在于，`deque`不会把现有元素复制到新的内存上，`deque`的元素也不是严格内存连续的，而是使用一系列大小固定的数组分块存储，所以`deque`的最小内存成本较大，不适合仅仅存储几个元素的情况。

时间复杂度:

* 随机访问: O(1)
* 插入删除双端元素: O(1)
* 插入删除中间元素: O(n)

### 1.4 forward_list(C++11)

...

### 1.5 list

...

## 2 Associative 非内存连续型

### 2.1 set

### 2.2 multiset

### 2.3 map

### 2.4 multimap

## 3 Unordered Associative 无序非内存连续型

### 3.1 unordered_set(C++11)

### 3.2 unordered_multiset(C++11)

### 3.3 unordered_map(C++11)

### 3.4 unordered_multimap(C++11)

## 4 Adaptors 适配器型

### 4.1 stack

### 4.2 queue

### 4.3 priority_queue

## 5 Views 视图型

### 5.1 span(C++20)
