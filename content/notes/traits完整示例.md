---
title: "Traits 完整示例"
author: ["ubuntu"]
date: 2025-11-07T11:39:00+08:00
draft: false
---

下面的例子展示 traits 在 C++ 泛型编程中为什么如此有价值。


## 场景：实现一个泛型的 “取值函数” get_value() {#场景-实现一个泛型的-取值函数-get-value}

我们希望它能同时支持：

-   普通类型（直接返回值）；

-   指针类型（自动解引用）；

-   智能指针（如 std::shared_ptr）；

-   并且写法优雅，不用写多重重载。


## 第一步：问题背景 {#第一步-问题背景}

如果我们用传统写法，可能要写：

```cpp
int get_value(int x) { return x; }
int get_value(int* x) { return *x; }
int get_value(std::shared_ptr<int> x) { return *x; }
```

这样代码臃肿、不灵活。想支持更多类型时，维护成本极高。这就是 traits 能大显身手的地方。


## 第二步：定义 traits {#第二步-定义-traits}

我们先定义一个模板结构体 value_traits，用来提取不同类型的“值类型”（value_type）。

<a id="code-snippet--traits实现"></a>
```cpp
#include <iostream>
#include <memory>
#include <type_traits>

// 默认情况：T 自身就是值类型
template <typename T>
struct value_traits {
    using value_type = T;

    static T& get(T& v) {
        return v;
    }
};

// 对指针类型进行偏特化
template <typename T>
struct value_traits<T*> {
    using value_type = T;

    static T& get(T* ptr) {
        return *ptr;
    }
};

// 对智能指针进行偏特化
template <typename T>
struct value_traits<std::shared_ptr<T>> {
    using value_type = T;

    static T& get(std::shared_ptr<T>& ptr) {
        return *ptr;
    }
};

```


## 第三步：使用 traits 写通用函数 {#第三步-使用-traits-写通用函数}

<a id="code-snippet--print-value函数实现"></a>
```cpp
template <typename T>
void print_value(T &&t) {
  using traits = value_traits<std::decay_t<T>>;
  using ValueType = typename traits::value_type;

  ValueType &v = traits::get(t);
  std::cout << "value = " << v << "\n";
}

```

这里通过 traits：

-   自动判断类型；

-   自动获取内部值；

-   无需关心类型是值、裸指针还是智能指针。


## 第四步：实际使用 {#第四步-实际使用}

```cpp
#include <iostream>
#include <memory>
#include <type_traits>

// 默认情况：T 自身就是值类型
template <typename T>
struct value_traits {
    using value_type = T;

    static T& get(T& v) {
        return v;
    }
};

// 对指针类型进行偏特化
template <typename T>
struct value_traits<T*> {
    using value_type = T;

    static T& get(T* ptr) {
        return *ptr;
    }
};

// 对智能指针进行偏特化
template <typename T>
struct value_traits<std::shared_ptr<T>> {
    using value_type = T;

    static T& get(std::shared_ptr<T>& ptr) {
        return *ptr;
    }
};

template <typename T>
void print_value(T &&t) {
  using traits = value_traits<std::decay_t<T>>;
  using ValueType = typename traits::value_type;

  ValueType &v = traits::get(t);
  std::cout << "value = " << v << "\n";
}

int main() {
  int a = 10;
  int *p = &a;
  auto sp = std::make_shared<int>(20);

  print_value(a);  // 普通变量
  print_value(p);  // 裸指针
  print_value(sp); // 智能指针
}
```

```text
value = 10
value = 10
value = 20
```


## 第五步：为什么在生产中有价值？ {#第五步-为什么在生产中有价值}

| 价值点         | 说明                                |
|-------------|-----------------------------------|
| ****统一接口**** | 函数 `print_value` 对各种类型都能工作，极大减少重载 |
| ****类型安全**** | 在编译期通过 `traits` 做类型推导，不会出错 |
| ****扩展性强**** | 新增类型（如 `unique_ptr` ）只需增加一个特化，不动主逻辑 |
| ****可读性高**** | 主逻辑表达“做什么”，traits 封装“怎么做” |
| ****零运行时开销**** | 所有决策在编译期完成，不影响性能    |


## 延伸：标准库中类似的实践 {#延伸-标准库中类似的实践}

标准库中也大量使用这种思路：

| Traits 名称            | 用途                                      |
|----------------------|-----------------------------------------|
| `std::iterator_traits` | 提取迭代器的 `value_type` 、 `difference_type` 等 |
| `std::char_traits`     | 定义字符的比较、拷贝逻辑（被 `std::basic_string` 使用） |
| `std::type_traits`     | 判断类型是否可拷贝、是否为整型、是否为指针等 |


## 小结 {#小结}

traits 是 模板元编程与泛型编程的桥梁👉 它的核心价值是：

> 把与类型相关的逻辑从算法中分离出来，让算法代码更简洁、更可扩展。

扩展：如何结合 enable_if 或 C++20 Concepts 让这个 traits 系统支持更多类型并提供编译期错误提示？
