---
title: "ANSI Common Lisp Notes"
author: ["ubuntu"]
date: 2025-11-04T20:05:00+08:00
draft: false
---

写一个函数，输入一个数 n ，返回把 n 与传入参数 (argument)相加的函数。

```lisp
; Lisp
(defun addn (n)
  #'(lambda (x)
      (+ x n)))

```

[Lisp 词法闭包]({{< relref "lisp_词法闭包.md" >}})
