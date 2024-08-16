---
title: C++ 析构函数与多态
date: 2023-08-01T10:33:00+08:00
categories: 
- C++
tags:
- C++
---

析构函数在很多地方上是特殊的，首先他是一个`noexcept`，所以不能在析构函数中抛出异常，在RAII设计中要考虑到这一点；其次在多态时，他的`virtual`关键字也是特殊的，并且不要添加`override`关键字，参考[C.128](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#c128-virtual-functions-should-specify-exactly-one-of-virtual-override-or-final)。

我们知道一个普通成员函数如果设置为`virtual`，在多态时调用这个函数会被子类函数替代，但在析构函数中，这样的逻辑是不正确的，所以`virtual`修饰析构函数时，是一个特殊的逻辑，不能和普通函数混为一谈。

那么什么是正确的逻辑？假设我们构造时的资源都要通过析构函数来回收，那么在C继承B继承A这种情况时，构造顺序是ABC，那么正确的析构顺序应该是CBA。如果不加关键字`virtual`，创建一个对象C然后再回收，实际上这种情况已经发生了，程序会依次调用CBA的析构函数。但在多态时，例如用B指针拿着C对象，构造时是依次调用ABC的构造函数，析构时只是依次调用BA的析构函数，C构造函数没有被调用，这样在大多数情况下都是不合理的。

这个问题引出了`virtual`关键字在析构函数上的使用，想要任何多态情况下都能全部依次调用析构函数，其实只需要在最顶部的基类上添加`virtual`即可，子类无需添加任何关键字。`virtual`关键字在析构函数上使用时，相当于一个开关，从添加了`virtual`的析构函数开始，只要其或其子类指针指向了其子类，都能够全部依次调用析构函数。如果自己和所有的父类都没有`virtual`析构函数，那么即便子类有`virtual`析构函数，在自己的多态时，只会从自己的析构函数开始依次调用。所以一条继承链中，只要有一个`virtual`即可。

示例代码，改变virtual的位置，代码会有不同的输出。

```cpp
class A
{
public:
    int val;
    A(int val) : val(val) {cout << val << "构造A" << endl;}
    A(const A& a) : val(a.val) {cout << val << "拷贝A" << endl;}
    A(A&& a) : val(a.val) {cout << val << "移动A" << endl;}
    virtual ~A() {cout << val << "析构A" << endl;}
};
/*
virtual 加在A类析构函数
123构造A
123构造B
123构造C
123析构C
123析构B
123析构A
*/

class B : public A
{
public:
    B(int val) : A(val) {cout << val << "构造B" << endl;}
    B(const B& a) : A(a.val) {cout << val << "拷贝B" << endl;}
    B(B&& a) : A(a.val) {cout << val << "移动B" << endl;}
    ~B() {cout << val << "析构B" << endl;}
};
/*
virtual 加在B类析构函数
123构造A
123构造B
123构造C
123析构C
123析构B
123析构A
*/

class C : public B
{
public:
    C(int val) : B(val) {cout << val << "构造C" << endl;}
    C(const C& a) : B(a.val) {cout << val << "拷贝C" << endl;}
    C(C&& a) : B(a.val) {cout << val << "移动C" << endl;}
    ~C() {cout << val << "析构C" << endl;}
};
/*
virtual 加在C类析构函数
123构造A
123构造B
123构造C
123析构B
123析构A
free(): invalid pointer
[1]    175872 abort (core dumped)  ./12
*/

int main(void)
{
    B* b = new C(123);
    delete b;

    return 0;
}
```