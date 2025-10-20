---
title: "C++ Primer Notes"
author: ["ubuntu"]
date: 2025-10-15T20:44:00+08:00
draft: false
---

## 第六章 函数 {#第六章-函数}


### 顶层const和底层const {#顶层const和底层const}

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


### 函数重载 {#函数重载}

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


### 内联函数和constexpr函数 {#内联函数和constexpr函数}


#### 函数调用的代价 {#函数调用的代价}

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


#### constexpr 函数 {#constexpr-函数}

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


### 函数指针 {#函数指针}

-   函数指针的类型由以下两方面共同决定：
    -   返回类型
    -   形参类型
    -   例如：
        ```cpp
        // 比较两个 string 对象的长度
        bool lengthCompare(const string&, const string&);

        // pf 指向一个函数,该函数的参数是两个 const string 的引用，返回值是 bool 类型。
        bool (*pf)(const string&, const string&);
        ```

<!--listend-->

-   函数指针的使用
    -   和数组一样，把函数名作为一个值使用时，该函数自动转换成指针，例如以下两条语句等价：
        ```cpp
        pf = lengthCompare;      // pf指向名为 lengthCompare 的函数
        pf = &lengthCompare;     // 等价的赋值语句
        ```

    -   我们可以直接使用指向函数的指针调用该函数，无需提前解引用指针：
        ```cpp
        bool b1 = pf("hello","goodbye");
        bool b2 = (*pf)("hello","goodbye");
        bool b3 = lengthCompare("hello","goodbye");
        ```

    -   不同函数类型的指针间不存在转换规则，我们可以为函数指针赋予一个 `nullptr` ，表示不指向任何指针。

-   重载函数的指针：函数指针必须与重载函数中的某一个精确匹配。

-   函数指针形参：和数组类似，虽然不能定义函数类型的形参，可以定义函数指针类型的形参。
    -   下面的写法像是函数类型，但其实是指针类型：
        ```cpp
        void useBigger(const string &s1, const string &s2, bool pf(const string &, const string &));
        ```

    -   下面是等价的声明：
        ```cpp
        void useBigger(const string &s1, const string &s2, bool (*pf)(const string &, const string &));
        ```

<!--listend-->

-   函数指针别名
    -   使用 `类型别名` 和 `decltype` 可以简化函数指针的声明。
        ```cpp
        // Func 和 Func2 是函数类型
        typedef bool Func(const string&, const string&);
        typedef decltype(lengthCompare)Func2;            // 等价的类型

        // FuncP 和 FuncP2 是指向函数的指针
        typedef bool(*FuncP)(const string&, const string&);
        typedef decltype(lengthCompare) *FuncP2;
        ```

    -   类型别名使用：
        ```cpp
        void useFunction(Func f) { // f 是一个函数
          cout << boolalpha << f("hello", "hi") << endl;
        }

        FuncP p1 = lengthCompare; // ✅ 函数名自动转换为指针
        FuncP2 p2 = &lengthCompare; // ✅ 也可以显式取地址

        // 以下两条函数声明等价：
        void useBigger(const string&, const string&, Func);
        void useBigger(const string&, const string&, Funcp2);
        ```

<!--listend-->

-   返回函数指针的函数
    -   使用 using 声明函数类型和函数指针类型
        ```cpp
        using F = int(int*, int);       // F是函数类型，不是指针类型
        using PF = int(*)(int*, int);   // PF是指针类型
        ```

    -   声明返回函数指针的函数
        ```cpp
        PF f1(int);   // 正确：PF是函数指针类型
        F f1(int);    // 错误：F是函数类型，f1不能返回一个函数
        F* f1(int);   // 正确：显示地指定返回类型是指向函数的指针
        ```

    -   直接声明 `f1`
        ```cpp
        int (*f1(int)) (int*, int);
        ```

        -   按照由内向外的顺序阅读上述声明语句：
            -   首先 f1 有一个形参列表，所以f1是一个函数；
            -   f1 前面有一个 `*` ，所以f1返回一个指针；
            -   指针的类型也包含形参列表，因此指针指向函数，该函数的返回类型是int

    -   使用尾置返回类型：
        ```cpp
        auto f1(int) -> int (*)(int*, int);
        ```

<!--listend-->

-   auto 和 decltype 用于函数指针类型
    -   假如我们知道返回的函数是哪一个，就可以使用 decltype 简化书写函数指针返回类型的过程：
        ```cpp

        ```
