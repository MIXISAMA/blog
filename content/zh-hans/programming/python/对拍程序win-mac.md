---
title: 对拍程序 win/mac
date: 2020-11-23T23:20:57+08:00
categories: 
- Python
tags:
- acm
---

刷题时肯定会遇到找不到wa点的情况，对拍程序就是用生成的数据分别在正确程序与自己错误程序上跑，对比是否一致，很快就能找出wa点。

<!-- more -->

## 1 目录结构

包含四个文件：

* ac.cpp: 标程
* wa.cpp: wa程
* gen.py: 数据生成器
* dp.bat/dp.bash: 对拍脚本

```txt
├─dp
│      ac.cpp
│      dp.bat
│      gen.py
│      wa.cpp
```

## 2 对拍脚本

### 2.1 dp.bat(windows)

```bat
@python gen.py > in.txt
@g++ ac.cpp -o ac.exe
@g++ wa.cpp -o wa.exe
@ac.exe < in.txt > out_ac.txt
@wa.exe < in.txt > out_wa.txt
@fc out_ac.txt out_wa.txt
```

### 2.2 dp.bash(mac)

```bash
python gen.py > in.txt
g++ ac.cpp -o ac
g++ wa.cpp -o wa
./ac < in.txt > out_ac.txt
./wa < in.txt > out_wa.txt
cmp out_ac.txt out_wa.txt
echo $?
```

## 3 使用方式

1. 将标程和wa程复制进ac.cpp和wa.cpp。
2. 自行构造数据生成器。
3. 在cmd中使用对拍指令dp.bat。或在terminal中使用bash dp.sh
4. 构造数据的输入在in.txt中。
5. 两个程序的输出在out_ac.txt与out_wa.txt中。

## 4 例子

最简单的A+B程序

### 4.1 ac.cpp

```ac.cpp
#include <iostream>
using namespace std;

int main(void) {
    int N;
    cin >> N;
    while ( N-- ) {
        int a, b;
        cin >> a >> b;
        cout << a + b << endl;
    }
    return 0;
}
```

### 4.2 wa.cpp

```ac.cpp
#include <iostream>
using namespace std;

int main(void) {
    int N;
    cin >> N;
    while (N--) {
        int a, b;
        cin >> a >> b;
        // 5个样例以后都是错的
        cout << a + b + N / 5 << endl;
    }
    return 0;
}
```

### 4.3 gen.py

```python
import random

T, N = 10, 10
MINA, MAXA = 0, 1000
print(N)
for _ in range(N):
    print(random.randint(MINA, MAXA), random.randint(MINA, MAXA))
```

### 4.4 开始对拍

#### 4.4.1 windows

```shell
dp.bat
正在比较文件 out_ac.txt 和 OUT_WA.TXT
***** out_ac.txt
1255
1969
1771
525
1260
1240
***** OUT_WA.TXT
1256
1970
1772
526
1261
1240
*****
```

#### 4.4.2 mac

```shell
bash dp.sh
out_ac.txt out_wa.txt differ: char 3, line 1
1
```
