---
title: "C++ 列表初始化"
author: ["ubuntu"]
date: 2025-11-08T13:06:00+08:00
draft: false
---

## C++列表初始化的常见用法 {#c-plus-plus-列表初始化的常见用法}

C++提供了一种\*更安全、更统一\*的初始化方法，尤其在初始化容器和防止类型窄化方面非常有用。

> 防止类型窄化的意思是：防止精度丢失，例如 double 转换为 int 可能损失信息。


### 什么是列表初始化 {#什么是列表初始化}

将 `std::initializer_list` 作为形参的构造函数是一种特殊的构造函数，能让一个类\*直接从花括号 {} 列表（即列表初始化）中创建对象\*。

简单来说，它的工作流程是：

1.  编译器看到代码 `ClassName obj = {a, b, c};` 。
2.  它会创建一个临时的、轻量级的 `std::initializer_list` 对象来包裹 ~{a, b, c}~。
3.  然后，它会寻找并调用 `ClassName` 中那个以 `std::initializer_list` 为参数的构造函数。


#### 示例：~std::vector~ 的工作原理 {#示例-std-vector-的工作原理}

你看似写的是：

```cpp
std::vector<int> v = {1, 2, 3};
```

实际上，编译器为你调用了 `std::vector` 的 `std::initializer_list` 构造函数，其声明类似：

```cpp
namespace std {
  class vector {
  public:
    // 这就是 std::initializer_list 构造函数
    vector(std::initializer_list<int> init);
    // ... 其他构造函数 ...
  };
}
```

编译器将 `{1, 2, 3}` 转换成一个 `std::initializer_list<int>` 对象，然后传递给这个构造函数。构造函数内部会遍历这个列表，并将元素添加到 `vector` 中。


#### 如何为自己的类创建它 {#如何为自己的类创建它}

你也可以为自己的类定义这种构造函数，让它支持 `{}` 初始化。

```cpp
#include <iostream>
#include <vector>
#include <initializer_list>

class MyVector {
public:
    // 关键！定义一个接受 std::initializer_list 的构造函数
    MyVector(std::initializer_list<int> list) {
        for (int x : list) {
            data.push_back(x);
        }
    }

    void print() {
        for (int x : data) {
            std::cout << x << " ";
        }
        std::cout << std::endl;
    }

private:
    std::vector<int> data;
};

int main() {
    MyVector mv = {10, 20, 30}; // 因为有了那个构造函数，这行代码才能工作
    mv.print(); // 输出: 10 20 30
    return 0;
}
```

\*总结\*：~std::initializer_list~ 构造函数是 C++11 引入的一个桥梁，它让 `{}` 这种简洁直观的语法能够被用于初始化任意支持它的类对象，尤其是标准库容器。


### 列表初始化的常见用法 {#列表初始化的常见用法}

1.  **初始化标准库容器 (最常用)**
    使用 `std::initializer_list` 构造函数，可以简洁地初始化容器。
    ```cpp
    #include <vector>
    #include <map>
    #include <string>

    std::vector<int> v = {1, 2, 3, 4, 5};
    std::map<std::string, int> capitals = {
        {"France", "Paris"},
        {"Japan", "Tokyo"}
    };
    ```

2.  **初始化聚合类型 (struct/class/array)**
    这是初始化普通 C 结构体或数组最直接的方式。
    ```cpp
    struct Point {
        int x;
        int y;
    };

    Point p = {10, 20}; // 初始化 p.x=10, p.y=20
    int arr[] = {1, 2, 3};
    ```

3.  **防止窄化转换 (提高代码安全性)**
    列表初始化不允许可能导致数据丢失的“窄化转换”，会在编译时报错。
    ```cpp
    double d = 3.14;
    // int i = d;      // 编译通过 (有警告)，i 的值为 3
    int j = {d};    // 编译错误！阻止了从 double 到 int 的窄化
    ```

4.  **在构造函数中初始化成员**
    在类的构造函数初始化列表中，统一使用 `{}` 风格。

    \#+begin_src cpp
    class MyClass {
    public:
        MyClass(int val) : member{val} {} // 使用 {} 初始化成员
    private:
        int member;
    };
