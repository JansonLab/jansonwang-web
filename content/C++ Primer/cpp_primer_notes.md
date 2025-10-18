---
title: "第六章 函数"
author: ["ubuntu"]
date: 2025-10-15T10:00:00+08:00
draft: false
---

## 顶层const和底层const {#顶层const和底层const}

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

<!--listend-->

-   顶层const不参与函数重载
    -   如下列函数重复定义了：
        ```cpp
        // 接受底层const
        int calc(const char *a, const char *b) {
          cout << "calc(const char* a, const char* b)" << endl;
          return 0;
        }

        // 顶层const不参与函数重载，所以跟上面的函数重复定义了。
        int calc(const char *const a, const char *const b) {
          cout << "calc(const char* a, const char* b)" << endl;
          return 0;
        }

        ```

    -   同样地下列函数也重复定义了：
        ```cpp
        int calc(char *a, char *b) {
          cout << "calc(char *a, char* b)" << endl;
          return 0;
        }

        // 顶层const不参与函数重载，所以跟上面的函数重复定义了。
        int calc(char *const a, char *const b) {
          cout << "calc(char *const a, char*const b)" << endl;
          return 0;
        }
        ```

    -   完整例子：
        ```cpp
        #include <iostream>

        using namespace std;

        int calc(const char *const a, const char *const b) {
          cout << "calc(const char* const a, const char* const b)" << endl;
          return 0;
        }

        int calc(char *const a, char *const b) { // 接受顶层
          cout << "calc(char *const a, char*const b)" << endl;
          return 0;
        }

        // 与 calc(char *const a, char *const b)
        // 构成重复定义，因为顶层const不参与函数重载
        // int calc(char *a, char *b) {
        //   cout << "calc(char *a, char* b)" << endl;
        //   return 0;
        // }

        // 与 calc(const char *const a, const char *const b)
        // 构成重复定义，因为顶层const不参与函数重载
        // int calc(const char *a, const char *b) { // 接受底层const
        //   cout << "calc(const char* a, const char* b)" << endl;
        //   return 0;
        // }

        int main() {
          char a;
          const char *const b = &a; // b 即是顶层const，也是底层const

          const char *c = &a; // 底层const

          char *const d = &a; // 顶层const

          const char *const e = &a;
          calc(c, c);
          calc(d, d);
          calc(e, e);
          return 0;
        }
        ```

        ```text
        calc(const char* const a, const char* const b)
        calc(char *const a, char*const b)
        calc(const char* const a, const char* const b)
        ```

<!--listend-->

-   顶层const和底层const的相互转换：
    -   底层不能直接转顶层:
        ```cpp
        char a;
        const char *c = &a; // 底层const
        // 底层const不能直接转顶层const，需要使用 const_cast
        //char *const f = c;
        char *const f = const_cast<char *>(c);
        ```

    -   顶层可以直接转底层：
        ```cpp
        char a;
        char *const d = &a; // 顶层const

        // 顶层const可以直接转底层const
        const char *g = d;
        ```

    -   顶层/底层可以直接转同时是顶层和底层
        ```cpp
        char a;
        const char *c = &a; // 底层const
        char *const d = &a; // 顶层const

        const char *const h = c;
        const char *const i = d;
        ```


## 函数重载 {#函数重载}

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


## 内联函数和constexpr函数 {#内联函数和constexpr函数}


### 函数调用的代价 {#函数调用的代价}

在C++中一个普通的函数调用主要有以下成本：

| 阶段   | 说明         | 是否可优化    |
|------|------------|----------|
| 参数传递 | 将实参复制到调用者栈帧 | 编译器可能用寄存器传参 |
| 返回地址保存 | 将下一条指令地址压入栈中 | 硬件自动完成，无法省略 |
| 栈帧建立 | 为被调用函数创建新的栈帧 | 可优化为尾调用或寄存器分配 |
| 函数体执行 | 执行函数体   | 必须          |
| 栈帧销毁 | 恢复调用者栈帧、返回 | 尾调用优化时可省略 |

内联函数可以避免函数调用的开销

-   将函数指定为内联函数(inline)，就是在每个调用点上"内联地"展开。
-   如下所示：我们将square_inline声明为内联函数
    ```cpp
    inline int square_inline(int x) {
        return x * x;
    }

    int main() {
        int a = 5;
        int r1 = a * a;          // 直接执行表达式
        int r2 = square_inline(a); // 函数调用
    }
    ```

    -   这样在计算 r2 时就不会产生函数调用开销，直接就可以计算 `a*a` 的值。
    -   注意：内联说明只是向编译器发出一个请求，编译器可以忽略这个请求。


### constexpr 函数 {#constexpr-函数}

-   constexpr 函数是指能用于常量表达式的函数。定义constexpr需要遵循以下约定：
    -   一个函数被声明为 `constexpr` ，意味着它的返回值必须能在编译期计算出来（当其参数是常量时）。
    -   函数的 `返回类型` 及所有 `形参类型` 都必须是 `字面值类型` 。
    -   函数体必须有且只有一条 `return语句` 。
    -   constexpr 函数被隐式地指定为内联函数。
    -   constexpr 函数可以包含其他语句，只要这学语句在运行时不执行任何操作，例如：空语句，类型别名，using 声明。

<!--listend-->

-   constexpt 代码示例：
    ```cpp
    constexpr int new_sz() {return 42;}
    constexpr int foo = new_sz();  // 正确：foo是一个常量表达式
    ```

<!--listend-->

-   constexpr 函数的返回值可以不是一个常量：
    ```cpp
    // 如果 arg 是常量表达式，则 scale(arg)也是常量表达式
    constexpr size_t scale(size_t cnt) { return new_se() * cnt; }

    int arr[scale(2)];      // 正确：scale(2)是常量表达式
    int i = 2;
    int a2[scale(i)];       // 错误：scale(i) 不是常量表达式
    ```

<!--listend-->

-   把内联函数和constexpr函数放到头文件内：
    -   和其他函数不一样，内联函数和 constexpr 函数可以定义多次，毕竟编译器需要获得函数定义，以便在函数调用出展开。
    -   对于给定的内联函数和 constexpr 函数，它的多个定义必须完全一致。
    -   基于以上两点：内联函数和 constexpr 函数通常定义在头文件中。
