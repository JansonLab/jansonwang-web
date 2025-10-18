---
title: "emacs变量作用域"
author: ["ubuntu"]
date: 2025-10-14T19:42:00+08:00
draft: false
---

## 作用域的概念 {#作用域的概念}

这段内容主要讲了如何为 Emacs Lisp 代码选择“方言”，这里的“方言”指的是变量作用域规则。

-   **两种方言**:
    -   \*现代方言\*：默认使用 \*词法作用域 (Lexical Binding)\*，更健壮、更推荐。
    -   \*旧式方言\*：默认使用 \*动态作用域 (Dynamic Binding)\*。

-   **如何选择**:
    -   通过一个名为 `lexical-binding` 的变量来控制。
    -   对于一个 `.el` 文件，必须在 **文件的第一行** 通过一个特殊的注释来设置它。

-   **具体设置方法**:
    -   \*启用词法作用域 (推荐)\*：在文件第一行写：
        ```emacs-lisp
        ;;; -*- lexical-binding: t; -*-
        ```

    -   \*使用旧式动态作用域\*：在文件第一行写：
        ```emacs-lisp
        ;;; -*- lexical-binding: nil; -*-
        ```

-   **默认行为**:
    -   如果文件里 \*没有这行声明\*，Emacs 会默认使用旧的动态作用域。
    -   但是，编译器会因此发出 \*警告\*，并且这个默认行为在未来可能会改变。

-   **其他情况**:
    -   在一些交互式环境中，词法作用域是 **默认启用** 的，比如：
        -   `*scratch*` 缓冲区
        -   `*ielm*` 缓冲区
        -   使用 `M-:= (~eval-expression`) 执行代码时


## Emacs Lisp 代码示例 {#emacs-lisp-代码示例}


### 显示图标 {#显示图标}

```emacs-lisp
(list
 (all-the-icons-octicon "gear")
 (all-the-icons-octicon "tag" :height 0.9 :v-adjust 0)
 (all-the-icons-octicon "file-binary"))
```
