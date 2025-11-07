---
title: "Traits 设计模式"
author: ["ubuntu"]
date: 2025-11-07T10:51:00+08:00
draft: false
---

在 C++ 中，traits（特征类） 是一种编程技巧（设计模式），主要用于在编译期获取类型信息或根据类型特征进行不同实现的选择。它通常以 模板类 的形式出现，能让模板代码更加通用、灵活和类型安全。


## traits 的本质 {#traits-的本质}

traits 本质上就是 “类型信息的提取器（type information extractor）”。

```cpp
#include <iostream>
using namespace std;

template <typename T>
struct MyTraits { static const bool is_pointer = false; };

template <typename T>
struct MyTraits<T *> {
  static const bool is_pointer = true;
};

int main() {
  std::cout << MyTraits<int>::is_pointer << "\n";   // 0
  std::cout << MyTraits<int *>::is_pointer << "\n"; // 1

  return 0;
}
```

这就是最典型的 traits 用法：通过模板偏特化，在编译期根据类型 T 的不同，定义不同的属性。


## 标准库中的 traits {#标准库中的-traits}

C++ 标准库中大量使用 traits，尤其是：


### `std::type_traits` {#std-type-traits}

提供一系列类型特征工具，用于在编译期判断类型性质：

```cpp
#include <type_traits>
#include <iostream>

int main() {
    std::cout << std::boolalpha;
    std::cout << std::is_integral<int>::value << "\n";       // true
    std::cout << std::is_pointer<int*>::value << "\n";       // true
    std::cout << std::is_same<int, long>::value << "\n";     // false
    std::cout << std::is_const<const int>::value << "\n";    // true
}
```

-   上述代码的优点是：
    -   在 编译期完成判断；
    -   可用于 SFINAE / Concepts / enable_if 等模板元编程。


### `iterator_traits` {#iterator-traits}

用于泛型算法中，统一访问迭代器的类型信息。

```cpp
template <typename Iterator>
void func(Iterator it) {
    using ValueType = typename std::iterator_traits<Iterator>::value_type;
    ValueType x = *it; // 可以正确推导出迭代器的元素类型
}
```

`std::iterator_traits` 内部定义了：

```cpp
template <class Iterator>
struct iterator_traits {
    using difference_type   = typename Iterator::difference_type;
    using value_type        = typename Iterator::value_type;
    using pointer           = typename Iterator::pointer;
    using reference         = typename Iterator::reference;
    using iterator_category = typename Iterator::iterator_category;
};
```


### traits 的作用总结 {#traits-的作用总结}

| 功能   | 说明                 |
|------|--------------------|
| 类型萃取 | 提取类型的特征（如值类型、指针类型等） |
| 类型判断 | 判断类型性质（如是否是整数、是否可拷贝） |
| 编译期分支 | 在模板中根据类型选择不同实现 |
| 泛型编程支持 | 让算法对不同类型的容器/迭代器都适用 |


### 典型应用示例 {#典型应用示例}

-   例1：根据类型选择不同实现
    ```cpp
    #include <type_traits>
    #include <iostream>

    template <typename T>
    void print(T t) {
        if constexpr (std::is_integral<T>::value)
            std::cout << t << " 是整数类型\n";
        else
            std::cout << t << " 不是整数类型\n";
    }

    int main() {
        print(42);
        print(3.14);
    }
    ```
    这里 if constexpr + traits 实现了 编译期分支选择。

<!--listend-->

-   例2：自定义traits 改变行为
    ```cpp
    template <typename T>
    struct PrintTraits {
      static void print(T t) { std::cout << t << '\n'; }
    };

    template <>
    struct PrintTraits<bool> {
      static void print(bool b) { std::cout << (b ? "true" : "false") << '\n'; }
    };

    int main() {
      PrintTraits<int>::print(10);    // 输出: 10
      PrintTraits<bool>::print(true); // 输出: true
    }
    ```


### traits 的实现核心 {#traits-的实现核心}

1.  模板偏特化（Partial Specialization）
    -   区分不同类型的实现。

<!--listend-->

1.  类型别名（using / typedef）
    -   暴露类型信息。

<!--listend-->

1.  静态常量或枚举
    -   提供编译期常量属性。

<!--listend-->

1.  SFINAE / enable_if / concepts
    -   在模板推导阶段进行条件选择。


### 总结 {#总结}

| 概念          | 说明                                                            |
|-------------|---------------------------------------------------------------|
| ****定义方式**** | 模板 + 偏特化                                                   |
| ****作用阶段**** | 编译期                                                          |
| ****用途****  | 类型提取、条件编译、泛型算法                                    |
| ****标准库例子**** | \`std::type_traits\`, \`std::iterator_traits\`, \`char_traits\` |
| ****核心思想**** | “让模板知道类型的特性”                                        |


### 实际例子 {#实际例子}

[Traits 完整示例]({{< relref "traits完整示例.md" >}})
