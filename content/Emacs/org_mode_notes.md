---
title: "ORG MODE Notes"
author: ["ubuntu"]
date: 2025-11-09T22:16:00+08:00
draft: false
---

## 格式化代码块 {#格式化代码块}


### lisp 代码 {#lisp-代码}

```lisp
(defun our-member (obj lst)
  (if (null lst)
      nil
      (if (eql (car lst) obj)
          lst
          (our-member obj (cdr lst)))))


(defun example-function (x y)
  (let ((result (+ x y)))
    (when (> result 10)
      (print "Large result"))
    result))
```

选中代码块，使用快捷键: `C-M \`


## 问题记录 {#问题记录}


### SLIME REPL 中执行 Common Lisp 代码卡顿问题 {#slime-repl-中执行-common-lisp-代码卡顿问题}

-   问题描述：当执行任何的输入命令时，例如 `(read)` ，当输入任何一个字符时，整个 Emacs 就会彻底卡死。

-   通过查询多个AI，最终 grok 给出了正确的答案。

-   解决方法：
    -   本问题由于自动补全插件干扰了SLIME的输入，导致卡顿。

    -   只需在 slime 的配置项中加入以下命令即可：

    -   (add-hook 'slime-repl-mode-hook (lambda () (company-mode -1)))

    -   这样,当启动 slime 时，不会有 company 的干扰。

> [自动不全导致SLIME REPL 卡顿原因](https://x.com/i/grok/share/Y8Ea6hUqQMOULla6vbUxRvNwl)


## 插入图片 {#插入图片}

-   插入图片

    <a id="figure--fig:example"></a>

    {{< figure src="/images/tmp.png" >}}

-   引用图片
    [1](#figure--fig:example)
