---
title: C++ CRTP 技巧
date: 2023-06-24T16:11:00+08:00
categories: 
- C++
tags:
- C++
- CRTP
---

C++ 实现派生类的静态函数返回类型为自己的类型，可以使用类模板和 CRTP（Curiously Recurring Template Pattern）技巧来实现这个需求：

```cpp
template <typename Derived>
class Base {
public:
    static Derived& get_instance() {
        return static_cast<Derived&>(*this);
    }
};

class Derived : public Base<Derived> {
public:
    void do_something() { /*...*/ }
};

int main() {
    Derived::get_instance().do_something();
    return 0;
}
```

这里，Base 类是一个模板类，并且它的派生类必须指定自己的类型作为模板参数。这样一来，Base 类就能够在静态函数中返回 Derived 类型的实例了。

注意到这里返回类型为引用类型，这是因为如果返回的是值类型，则会发生切片问题，导致返回的是 Base 类型而不是 Derived 类型。通过返回引用类型，可以避免这个问题。同时，由于需要在静态函数中访问对象，因此也需要使用 static_cast 将 this 指针转换为派生类的类型。

最后，在主函数中，我们可以通过调用 get_instance 函数获取 Derived 类型的实例，并调用其成员函数 do_something。
