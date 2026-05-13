---
title: "const成员函数"
author: ["Janson"]
date: 2026-05-13T17:05:00+08:00
draft: false
---

在C++中，const成员函数是指在函数声明和定义末尾加上const关键字的成员函数。它向编译器和使用者承诺：​​这个函数不会修改当前对象的状态​​（即对象的成员变量）


## 语法 {#语法}

在 C++ 中，函数名后加 const，其实是修饰成员函数的，完整写法一般是这样的：

```cpp
class MyClass {
public:
    int getValue() const;  // 这里的 const 修饰的是函数
};
```

这一行的 const 并不是修饰返回值或参数，而是修饰隐含的 this 指针，意思是：

> 在这个成员函数中，不能修改类的成员变量（除非它们被声明为 mutable）。
>
> [mutable修饰成员变量]({{< relref "mutable修饰成员变量.md" >}})


## 原理 {#原理}

每个非静态成员函数，实际上都有一个隐含参数 `this 指针` ，它的类型如下：

```cpp
ClassName* const this;
```

而当你在函数名后加上 const 时，这个隐含参数变成了：

```cpp
const const ClassName* const this;
```


## 进阶（重载） {#进阶-重载}

`const 成员函数` 还能参与函数重载：

```cpp
#include <iostream>

class Test {
public:
  int &value() { // 非 const 版本
    std::cout << "非 const 版本 " << std::endl;
    return val;
  }

  int value() const { // const 版本
    std::cout << "const 版本 " << std::endl;
    return val;
  }

private:
  int val = 42;
};

int main() {
  Test a;
  const Test b;

  a.value() = 10;         // 调用非 const 版本，可修改
  b.value(); // 调用 const 版本，只能读
}
```

```text
非 const 版本
const 版本
```


## 总结 {#总结}

> 函数名后加 `const` 表示这个函数不会修改对象状态，即不能修改非 `mutable` 成员，也不能调用非 `const` 成员函数。它使代码更安全，也能让 const 对象正常调用这个函数。


## const成员函数与\*const {#const成员函数与-const}

假设一个成员函数被声明为 const 成员函数，如果它返回 `*this` ，那 `*this` 是什么类型？

```cpp
#include <iostream>
using namespace std;

class Person {
  string name;
  int x, y;

  Person():x(0),y(0){};
  Person(int _x, int _y) : x(_x), y(_y) {}

  Person &move(int _x, int _y) {
    x = _x;
    y = _y;
    return *this;
  }

  const Person &show() const{
    cout << x <<" " << y<<endl;
    return *this;
  }
};

int main() {
  cout << "Hello world" << endl;
  return 0;
}
```
