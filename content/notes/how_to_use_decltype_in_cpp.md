---
title: "How to use decltype in c++"
author: ["ubuntu"]
date: 2025-10-20T19:59:00+08:00
draft: false
---

[C++ Primer Notes]({{< relref "cpp_primer_notes.md" >}})

decltype是 C++11 引入的关键字，用于在编译时查询表达式或实体的类型。

[元宝DeepSeek](https://yuanbao.tencent.com/bot/app/share/chat/f8RYfhRSNAYu)

以下是一些使用示例：

| 表达式                                  | decltype 推导结果   | 推导规则说明                  |
|--------------------------------------|-----------------|-------------------------|
| int var;decltype(var)                   | int                 | 变量名（标识符）直接推导为该变量的声明类型 |
| const int&amp;&amp; fx();decltype(fx()) | const int&amp;&amp; | 函数调用推导为函数返回值的类型（包括值类别和cv限定符） |
| int n;decltype((n))                     | int&amp;            | 表达式是左值（如被括号包围的变量名），推导为类型的左值引用 |
| int n;decltype(++n)                     | int&amp;            | 前缀递增等返回左值引用的操作，推导为左值引用 |
| int n;decltype(n++)                     | int                 | 后缀递增等返回右值的操作，推导为值类型 |
|                                         |                     |                               |
|                                         |                     |                               |

> [C++中前置++和后置++的区别](https://wangjunstf.github.io/2025/10/01/cpp-qian-zhi-jia-jie-he-hou-zhi-jia-jia-de-qu-bie/)
