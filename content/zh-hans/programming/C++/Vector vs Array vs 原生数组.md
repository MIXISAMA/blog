---
title: Vector vs Array vs 原生数组
date: 2023-08-13T15:28:00+08:00
categories: 
- C++
tags:
- C++
- STL
---

这里对三种类型做一个读取与写入的性能测试。写入1e8的数据，然后再读取，看一下哪个快。

本来以为速度从快到慢依次是`raw > array > vector`，实际上结果如下：

* O0 `raw > vector > array`
* O3 `raw = vector = array`

从安全性来说array比raw更好，如果使用O3编译，两者性能一致，所以array相比raw是一个更优的选择。

```cpp
#include <iostream>
#include <array>
#include <vector>
#include <chrono>
using namespace std;
using namespace std::chrono;

template <typename T>
long long demo(T& arr, int size, int delta)
{
    for (int i = 0; i < size; i++) {
        arr[i] = i + delta;
    }
    long long sum = 0;
    for (int i = 0; i < size; i++) {
        sum += arr[i];
    }
    return sum;
}

const int N = 100000000;

long long v1[N];
array<long long, N> v2;
vector<long long> v3;

// 全部使用堆空间，结果相同
/*
long long* v1 = new long long[N];
array<long long, N>& v2 = *new array<long long, N>;
vector<long long> v3;
*/

int main(void)
{
    v3.resize(N);
    long long res;
    steady_clock::time_point start, over;

    cout << "case 1" << endl;

    start = steady_clock::now();
    res = demo(v1, N, 1);
    over  = steady_clock::now();
    cout << "raw array : " << res << " -> " << (over - start).count() / 1000000 << "ms" << endl;

    start = steady_clock::now();
    res = demo(v2, N, 1);
    over  = steady_clock::now();
    cout << "stl array : " << res << " -> " << (over - start).count() / 1000000 << "ms" << endl;

    start = steady_clock::now();
    res = demo(v3, N, 1);
    over  = steady_clock::now();
    cout << "stl vector: " << res << " -> " << (over - start).count() / 1000000 << "ms" << endl;

    cout << "case 2" << endl;

    start = steady_clock::now();
    res = demo(v1, N, 2);
    over  = steady_clock::now();
    cout << "raw array : " << res << " -> " << (over - start).count() / 1000000 << "ms" << endl;

    start = steady_clock::now();
    res = demo(v2, N, 2);
    over  = steady_clock::now();
    cout << "stl array : " << res << " -> " << (over - start).count() / 1000000 << "ms" << endl;

    start = steady_clock::now();
    res = demo(v3, N, 2);
    over  = steady_clock::now();
    cout << "stl vector: " << res << " -> " << (over - start).count() / 1000000 << "ms" << endl;

    cout << "case 3" << endl;

    start = steady_clock::now();
    res = demo(v1, N, 3);
    over  = steady_clock::now();
    cout << "raw array : " << res << " -> " << (over - start).count() / 1000000 << "ms" << endl;

    start = steady_clock::now();
    res = demo(v2, N, 3);
    over  = steady_clock::now();
    cout << "stl array : " << res << " -> " << (over - start).count() / 1000000 << "ms" << endl;

    start = steady_clock::now();
    res = demo(v3, N, 3);
    over  = steady_clock::now();
    cout << "stl vector: " << res << " -> " << (over - start).count() / 1000000 << "ms" << endl;

    return 0;
}
```

在`-O0`时

```txt
case 1
raw array : 5000000050000000 -> 431ms
stl array : 5000000050000000 -> 642ms
stl vector: 5000000050000000 -> 378ms
case 2
raw array : 5000000150000000 -> 272ms
stl array : 5000000150000000 -> 489ms
stl vector: 5000000150000000 -> 376ms
case 3
raw array : 5000000250000000 -> 271ms
stl array : 5000000250000000 -> 486ms
stl vector: 5000000250000000 -> 373ms
```

在`-O2`时
```txt
case 1
raw array : 5000000050000000 -> 217ms
stl array : 5000000050000000 -> 231ms
stl vector: 5000000050000000 -> 100ms
case 2
raw array : 5000000150000000 -> 100ms
stl array : 5000000150000000 -> 93ms
stl vector: 5000000150000000 -> 93ms
case 3
raw array : 5000000250000000 -> 94ms
stl array : 5000000250000000 -> 94ms
stl vector: 5000000250000000 -> 93ms
```

在`-O3`时

```txt
case 1
raw array : 5000000050000000 -> 196ms
stl array : 5000000050000000 -> 195ms
stl vector: 5000000050000000 -> 82ms
case 2
raw array : 5000000150000000 -> 81ms
stl array : 5000000150000000 -> 79ms
stl vector: 5000000150000000 -> 78ms
case 3
raw array : 5000000250000000 -> 80ms
stl array : 5000000250000000 -> 81ms
stl vector: 5000000250000000 -> 80ms
```

由于有缓存命中，在第一个用例，vector resize以后所有的成员已经在缓存中了，而raw和array成员没有有在缓存中，所以vector更快。
