---
title: "Common Lisp 符号|绑定|变量"
author: ["ubuntu"]
date: 2025-11-10T11:31:00+08:00
draft: false
---

> [Common Lisp 符号](https://yuanbao.tencent.com/bot/app/share/chat/Dt24gBbASvqG)
>
> [Common Lisp 符号与变量的关系](https://chatgpt.com/s/t_691161af04d481919e00683f4411496c)


## 一、符号（Symbol） {#一-符号-symbol}

在 Common Lisp 中，符号（Symbol）​ 是一种基本的数据类型，具有唯一性，常用作标识符（如变量名、函数名）。

-   符号的特点是：
    -   具有名称（可打印表示）。
    -   可能属于某个包（Package，即命名空间）。
    -   可绑定值（作为变量）、函数定义或其他属性。

<!--listend-->

-   常见符号示例
    1.  关键字符号（以冒号开头，自求值为其本身）：
        :key、:test、:allow-other-keys
        （多用于函数的关键字参数）

    2.  标准库中的函数/宏名：
        car、cdr、cons、list、defun、if、let
        （这些符号在 COMMON-LISP包中，通常通过包名隐式引用）

    3.  常量符号：
        pi、t（真值）、nil（假值/空表）

    4.  特殊变量（通常用\*包围）：
        **standard-output\*、\*package\*、\*read-table**

    5.  包名：
        COMMON-LISP、COMMON-LISP-USER、KEYWORD

示例代码：

```lisp
;; 符号作为变量名
(setq x 10)     ; 符号 x 绑定到值 10

;; 符号作为函数名
(defun add (a b)
  (+ a b))      ; 符号 add 绑定到一个函数

;; 关键字符号的用法
(find 3 '(1 2 3) :from-end t)  ; :from-end 是关键字符号

;; 检查符号是否属于某个包
(symbol-package 'car)          ; 返回 COMMON-LISP 包

;; 符号的名称
(symbol-name 'hello)           ; 返回字符串 "HELLO"
```

符号本身是一个对象（和字符串、数字一样），具有以下几个重要组成部分：

| 属性             | 含义            |
|----------------|---------------|
| ****name****     | 符号的名字（字符串） |
| ****value****    | 与该符号关联的变量值（若有） |
| ****function**** | 与该符号关联的函数定义（若有） |
| ****plist****    | 属性表（任意键值对） |
| ****package****  | 符号所属的包（命名空间） |

可以用 Lisp 提供的函数查看这些属性：

```lisp
(symbol-name 'foo)       ; => "FOO"
(symbol-value 'foo)      ; => 获取符号的全局值（若有）
(symbol-function 'foo)   ; => 获取符号绑定的函数
(symbol-plist 'foo)      ; => 获取符号的属性表
(symbol-package 'foo)    ; => 获取符号所在包
```


## 二、变量 {#二-变量}

变量是符号在某个环境（作用域）中被赋予的一个“值绑定（binding）”。例如：

```lisp
(let ((x 10))
  (+ x 1))
```

-   x 是一个符号；

-   在 let 的作用域内，x 被绑定为一个局部变量；

-   当 Lisp 解释器看到 x 时，它会查找这个符号在当前环境下的绑定(binding)，找到对应的值(10)。


## 三、 符号与变量的关系 {#三-符号与变量的关系}

> 符号是名字，变量是绑定。

-   符号是“名字对象”，它可以出现在程序中；

-   变量是某个“名字（符号）”在特定环境中所指向的值。

例如：

```lisp
(setq a 42)
(princ (symbol-value 'a))
```

```text
42
```

-   a 是符号

-   (setq a 42) 在全局环境中给符号 a 的 value cell 绑定了一个值。

-   现在(symbol-value 'a) 的结果就是 42。

另一个例子：

```lisp
(let ((a 1))
  (princ a))
```

```text
1
```

-   符号 a 本身不变。

-   let 建立了一个局部绑定，使得 a 在 let 内指向了值 1。

-   这个绑定只在 let 作用域内有效，离开后全局值（如果有）恢复。


## 四、 函数绑定与变量绑定的区别 {#四-函数绑定与变量绑定的区别}

在 Common Lisp 中，符号有两个命名空间（two namespaces）：

1.  变量命名空间（value cell）

2.  函数命名空间（function cell）

这意味着一个符号可以同时作为变量名和函数名存在而互不冲突：

```lisp
(setq foo 10)          ; 符号 foo 的变量值是 10
(defun foo (x) (+ x 1)) ; 符号 foo 的函数定义

(princ (list (symbol-value 'foo) (funcall (symbol-function 'foo) 5)))
```

```text
(10 6)
```

在这种情况下，foo 即是一个变量名，也是一个函数名，各自有独立的存储。


## 五、总结 {#五-总结}

可以把符号想象成一个“带着多个槽位的对象”：

```latex
Symbol: FOO
 ├─ name: "FOO"
 ├─ value: 42               ← 变量绑定（value cell）
 ├─ function: #<FUNCTION>   ← 函数绑定（function cell）
 ├─ plist: (...)            ← 属性表
 └─ package: COMMON-LISP-USER
```

| 概念 | 本质                   | 存储位置             | 是否全局 | 访问方式                       |
|----|----------------------|------------------|------|----------------------------|
| 符号 | Lisp对象               | 内存中的符号表       | 是       | 'foo                           |
| 变量 | 符号在某作用域的值绑定 | 环境栈（或符号的 value cell） | 有全局/局部之分 | foo                            |
| 函数 | 符号的 function cell 中的绑定 | 函数环境             | 有全局/局部之分 | (foo ...) 或 (funcall 'foo ...) |

> 在更高级的 Lisp 用法中，这种“符号-绑定”机制支撑了：
>
> -   动态作用域（special variables）
>
> -   宏展开时的符号捕获与遮蔽
>
> -   多命名空间设计（函数 vs 变量）
>
> -   符号宏（symbol-macro）
>
> -   编译时与运行时环境区分
