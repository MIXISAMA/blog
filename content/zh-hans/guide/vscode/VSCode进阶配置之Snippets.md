---
title: VSCode 进阶配置之 Snippets
date: 2020-11-25T00:31:18+08:00
categories: 
- VSCode
tags:
- VSCode
- acm
---

## 1 简述

每次都要复制代码头仍然是个痛点，所以可以利用vscode的snippets自动生成模板。

<!-- more -->

## 2 配置snippets

在`.vscode`目录中创建`snippet.code-snippets`文件，里面这样写。

```json
{
    "acmhead": {
        "prefix": "acmhead",
        "body": [
            "#include <bits/stdc++.h>",
            "using namespace std;",
            "// #define __DEBUG__",
            "#ifdef __DEBUG__",
            "#include \"mylog.h\"",
            "#else",
            "#define LOG(format,...)",
            "#define LOGV(v)",
            "#define LOGM(m)",
            "#endif",
            "",
            "int main(void){",
            "\t$1",
            "\treturn 0;",
            "}"
        ],
        "description": "acm common head"
    }
}
```

## 3 使用方法

新建一个cpp文件，输入`acmhead`回车，便可自动键入模板。如下。

```C++
#include <bits/stdc++.h>
// using namespace std;
// #define __DEBUG__
#ifdef __DEBUG__
#include "mylog.h"
#else
#define LOG(format, ...)
#define LOGV(v)
#define LOGM(m)
#endif

int main(void){
    LOG("123%d", 4);
    return 0;
}
```
