---
title: "Common Lisp 打印方法总结"
author: ["ubuntu"]
date: 2025-11-09T21:38:00+08:00
draft: false
---

## print | prin1 | princ {#print-prin1-princ}


### print {#print}

打印对象，并在前面加一个换行，后面加一个空格。输出是“可读的”（Readably），即 Lisp 读取器可以重新读入。字符串会带上引号。

```lisp
(print "Hello")
```

```text

"Hello"
```


### prin1 {#prin1}

类似 print，但不加换行和空格。输出也是可读的。

```lisp
(prin1 "Hello")
```

```text
Hello
```


### princ {#princ}

打印对象，但输出是“人类可读的”（Human-readable）。不添加引号等，适合直接显示给用户。

```lisp
(princ "hello")
```

```text
hello
```


## format {#format}

这是最强大、最灵活的格式化输出函数，类似 C 的 printf。

-   第一个参数是输出流（\`t\` 代表标准输出）。

-   第二个参数是格式化控制字符串。

-   后面的参数是待格式化的数据。

<!--listend-->

-   常用指令：
    -   ~a: 美学模式（Aesthetic），类似 \`princ\`。
    -   ~s: 标准模式（Standard），类似 \`prin1\`。
    -   ~d: 十进制整数。
    -   ~%: 换行。
    -   ~&amp;: 新起一行（如果不在行首，则换行）。

<!--listend-->

```lisp
(format t "Hello, ~a! The number is ~d.~%" "World" 42)
```

```text
Hello, World! The number is 42.
```


## pprint {#pprint}

用于美观地打印复杂的 Lisp 对象（如列表、结构体），会自动进行缩进和换行，非常适合调试。

```lisp
(pprint '(defun factorial (n) (if (zerop n) 1 (* n (factorial (1- n))))))
```

```text

(DEFUN FACTORIAL (N)
  (IF (ZEROP N)
      1
      (* N (FACTORIAL (1- N)))))
```


## write 系列 {#write-系列}

-   write-line: 打印一个字符串并加上换行。

-   write-string: 只打印字符串。

-   write-char: 打印单个字符。

<!--listend-->

```lisp
(write-line "This is a line.")
```

```text
This is a line.
```


## 总结 {#总结}

-   调试简单值：用 print。

-   向用户显示字符串：用 princ 或 (format t "~a" ...)。

-   构建复杂字符串或格式化输出：用 format。

-   查看复杂数据结构：用 pprint。
