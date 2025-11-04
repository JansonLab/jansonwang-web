---
title: "右值引用 std::move std::forward"
author: ["ubuntu"]
date: 2025-10-21T14:58:00+08:00
draft: false
---

## 右值引用 {#右值引用}

右值引用是一种新类型的引用，用来绑定到右值（临时对象）。举例：

```cpp
#include <iostream>
#include <utility>
using namespace std;

void process(int& x) { cout << "左值引用\n"; }
void process(int&& x) { cout << "右值引用\n"; }

int main() {
    int a = 10;
    process(a);       // 左值引用版本
    process(20);      // 右值引用版本
}
```

```text
左值引用
右值引用
```


## std::move {#std-move}

当我们将std::move作用于一个对象时，意味着告诉编译器“我不再需要这个对象，你可以移动它的资源”。示例：

```cpp
#include <iostream>
#include <vector>
#include <utility>
using namespace std;

int main() {
    vector<int> v1 = {1, 2, 3};
    vector<int> v2 = std::move(v1); // 移动而非复制

    cout << "v1.size() = " << v1.size() << endl;
    cout << "v2.size() = " << v2.size() << endl;
}
```

```text
v1.size() = 0
v2.size() = 3
```

-   核心流程：
    -   `std::move(v1)` ---&gt; 把 `v1` 转换为右值引用类型。
    -   调用 `vector` 的移动构造函数，把内部资源转移到v2。
    -   之后 v1 处于“有效但未指定”状态（通常为空）。

<!--listend-->

-   一个典型的移动构造函数如下：
    ```cpp
    #include <iostream>
    #include <cstring>

    class MyString {
    public:
        // 普通构造函数
        MyString(const char* str = "") {
            std::cout << "普通构造函数" << std::endl;
            length = strlen(str);
            data = new char[length + 1];
            strcpy(data, str);
        }

        // 移动构造函数
        MyString(MyString&& other) noexcept
            : data(other.data), length(other.length) {  // 1. 接管资源
            std::cout << "移动构造函数" << std::endl;

            // 2. 置空源对象
            other.data = nullptr;
            other.length = 0;
        }

        // 析构函数
        ~MyString() {
            delete[] data;
        }

        // 获取字符串内容
        const char* c_str() const {
            return data ? data : "";
        }

    private:
        char* data;
        size_t length;
    };
    ```


## std::forward {#std-forward}

`std::forward` 根据实参的值类别（左值或右值），决定是否把参数转发为右值。

-   不使用 `std::forward` 时：
    ```cpp
    #include <iostream>
    using namespace std;

    void foo(int& x) { cout << "左值版本\n"; }
    void foo(int&& x) { cout << "右值版本\n"; }

    template<typename T>
    void wrapper(T&& arg) {
        foo(arg); // 总是左值！即使传进来的是右值
    }

    int main() {
        int a = 10;
        wrapper(a);     // 左值
        wrapper(20);    // 右值
    }
    ```

    ```text
    左值版本
    左值版本
    ```

    无论外部传入的是左值还是右值，函数内部的具名参数 arg本身都是一个表达式，而所有具名变量表达式都是左值​​。

<!--listend-->

-   使用 `std::forward`
    ```cpp
    #include <iostream>
    using namespace std;

    void foo(int &x) { cout << "左值版本\n"; }
    void foo(int &&x) { cout << "右值版本\n"; }
    template <typename T> void wrapper(T &&arg) {
      foo(std::forward<T>(arg)); // 根据T是否为左值/右值，完美转发
    }

    int main() {
      int a = 10;
      wrapper(a);  // 左值
      wrapper(20); // 右值
    }
    ```

    ```text
    左值版本
    右值版本
    ```


## 对比表 {#对比表}

| 名称                     | 功能       | 是否移动资源 | 常见用途    | 关键语义 |
|------------------------|----------|--------|---------|------|
| T&amp;&amp;              | 接受右值引用 | 否     | 移动构造函数、移动赋值 | 区分左值/右值 |
| std::move(x)             | 将左值强制转换为右值 | 否     | 调用移动构造/赋值 | 我不再用这个对象 |
| std::forwart&lt;T&gt;(x) | 条件转发   | 否     | 模板完美转发 | 根据参数原貌转发 |


## 综合示例 {#综合示例}

下面是一个典型的移动语义+完美转发的例子：

```cpp
#include <iostream>
#include <string>
#include <utility>
using namespace std;

struct Person {
    string name;

    Person(string n) : name(std::move(n)) { // 移动构造参数 n
        cout << "构造 Person: " << name << endl;
    }
};

template <typename T>
void makePerson(T&& arg) {
    Person p(std::forward<T>(arg)); // 完美转发
}

int main() {
    string s = "Jason";
    makePerson(s);          // 左值调用
    makePerson("Tech");     // 右值调用
}

```

```text
构造 Person: Jason
构造 Person: Tech
```
