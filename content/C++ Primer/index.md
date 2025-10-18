---
title: "C++ Primer Notes"
author: ["ubuntu"]
draft: false
---

<div class="ox-hugo-toc toc has-section-numbers">

<div class="heading">Table of Contents</div>

- <span class="section-num">1</span> [第六章 函数](#第六章-函数)
    - <span class="section-num">1.1</span> [顶层const和底层const](#顶层const和底层const)
    - <span class="section-num">1.2</span> [函数重载](#函数重载)

</div>
<!--endtoc-->


## <span class="section-num">1</span> 第六章 函数 {#第六章-函数}


### <span class="section-num">1.1</span> 顶层const和底层const {#顶层const和底层const}

-   核心区别：
    -   顶层const：
        -   指对象本身是常量
        -   修饰变量本身
    -   底层const：
        -   指针或引用所指向的对象是常量
        -   修饰指针或引用指向的对象：表示不能通过指针或引用修改其指向的对象

<!--listend-->

-   示例：
    ```cpp
    int i = 0;

    // 顶层 const
    const int ci = 42;         // ci 的值不能改变
    int* const p1 = &i;        // p1 的指向不能改变（永远指向i）

    // 底层 const
    const int* p2 = &ci;       // 不能通过 p2 修改 ci 的值
    const int& r1 = ci;        // 不能通过 r1 修改 ci 的值

    // 既是顶层又是底层 const
    const int* const p3 = &ci; // p3 的指向和它所指对象的值都不能改变
    ```


### <span class="section-num">1.2</span> 函数重载 {#函数重载}

-   对于重载的函数：
    1.  形参数量不同。
    2.  形参类型不同。

<!--listend-->

-   `const 形参` 和重载：
    -   一个拥有顶层 const 的形参无法和另一个没有顶层 const 的形参区分开来。
        ```cpp
        // 以下两个函数无法重载区分
        Record lookup(Phone);
        Record lookup(const Phone);   // 顶层const

        // 以下两个函数无法重载区分
        Record lookup(Phone*);
        Record lookup(Phone* const);  // 顶层const
        ```

    -   如果形参是某种类型的指针或引用，则通过其指向的是常量对象还是非常量对象来实现函数重载，此时的 const 是底层的。以下是四个同名的重载函数：
        ```cpp
        Record lookup(Account &);             // 作用于 Account 的引用
        Record lookup(const Account &);       // 作用于常量引用
        Record lookup(Account *);             // 作用于指向 Account 的指针
        Record lookup(const Account *);       // 作用于指向常量的指针
        ```

-   `const_cast` 和重载
    -   我们可以利用 const_cast 来根据实参的类型来返回相对应的类型，例如：实参是const，返回const引用，当实参是普通对象，返回普通引用。
        ```c
        // 比较两个 string 对象的长度，返回较短的那个引用
        const string &shorterString(const string &s1, const string &s2){
          return s1.size() <= s2.size() ? s1 : s2;
        }

        string &shorterString(string &s1, string &s2){
          auto &r = shorterString(const_cast<const string&>(s1),const_cast<const string&>(s2));
          return const_cast<string&>(r);
        }
        ```

        -   当实参是 `const 对象` 时，调用第一个函数，并返回 `const 引用` 。
        -   当实参是 `普通对象` 时，调用第二个函数，并返回 `普通引用` 。
