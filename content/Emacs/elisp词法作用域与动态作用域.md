
---
title: "Emacs Lisp 词法作用域与动态作用域"
author: ["ubuntu"]
date: 2025-10-16T13:20:00+08:00
draft: false
---

## 概念区别 {#概念区别}

| 对比点    | 动态作用域      | 词法作用域                                         |
|--------|------------|-----------------------------------------------|
| 变量查找方式 | 在调用链中查找变量值 | 在定义处环境中查找变量值                           |
| 变量查找方式 | 在 `调用链` 中查找变量值 | 在 `定义处环境` 中查找变量值                       |
| 变量的绑定生效范围 | 从绑定点开始，直到函数调用结束 | 从定义时的代码块开始，直到离开该词法块             |
| 可预测性  | 较低（取决于调用者上下文） | 较高（取决于代码结构）                             |
| Elisp默认模式 | 旧版本默认动态作用域 | Emacs 24+ 可通过 \`(setq lexical-binding t)\` 启用词法作用域 |
| 常见用途  | 调试、临时变量、hook机制 | 高阶函数、闭包、模块化编程                         |


## 举例说明 {#举例说明}


### 动态作用域示例（默认Elisp行文） {#动态作用域示例-默认elisp行文}

```emacs-lisp
(setq x 10)

(defun foo ()
  (message "foo: x = %d" x))

(defun bar ()
  (let ((x 20))
    (foo)))  ;; 调用 foo 时 x 的值为 20

(bar)

```

```text
foo: x = 20
```

-   解释：
    -   `foo` 定义时没有局部变量 `x` 。
    -   `bar` 在调用时用 `let` 绑定了 `x=20` 。
    -   由于 Elisp 默认使用动态作用域（在调用链上查找变量），foo 找到了 bar 的局部 x，于是输出 20。


### 词法作用域示例（开启lexical-binding） {#词法作用域示例-开启lexical-binding}

-   启用词法作用域
    -   在 `*.el` 文件或缓冲区的顶部加入：
        ```emacs-lisp
        ;; -*- lexical-binding: t -*-
        ```

    -   通过以下指令： `(setq lexical-binding t)`

<!--listend-->

-   代码示例
    ```emacs-lisp
    (setq lexical-binding t)
    (setq x 10)

    (defun foo ()
      (message "foo: x = %d" x))

    (defun bar ()
      (let ((x 20))
        (foo)))  ;; 调用 foo 时 x 的值为多少？

    (bar)
    ```

    ```text
    foo: x = 10
    ```
