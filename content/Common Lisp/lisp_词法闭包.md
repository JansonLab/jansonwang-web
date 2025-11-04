---
title: "Lisp 词法闭包"
author: ["ubuntu"]
date: 2025-11-04T20:15:00+08:00
draft: false
---

## 什么是词法闭包 {#什么是词法闭包}

-   词法闭包包含三个关键要素：
    1.  函数：一段可执行的代码
    2.  环境：函数被创建时的词法作用域
    3.  绑定：函数可以访问和修改其创建时的局部变量


## 计数器闭包 {#计数器闭包}

```lisp
;; 定义一个创建闭包的函数
(defun make-counter (initial-value)
  "返回一个闭包，该闭包捕获了变量count"
  (let ((count initial-value))  ; 局部变量count被闭包捕获
    (lambda ()                  ; 返回一个匿名函数（闭包）
      (incf count))))           ; 这个函数可以访问和修改count

;; 使用闭包
(let ((my-counter (make-counter 0)))  ; 创建闭包实例
  (format t "第一次调用: ~a~%" (funcall my-counter))  ; 输出 1
  (format t "第二次调用: ~a~%" (funcall my-counter))  ; 输出 2
  (format t "第三次调用: ~a~%" (funcall my-counter))) ; 输出 3

;; 创建另一个独立的闭包实例
(let ((another-counter (make-counter 10)))
  (format t "另一个计数器: ~a~%" (funcall another-counter))  ; 输出 11
  (format t "再调用一次: ~a~%" (funcall another-counter)))    ; 输出 12
```


## 更复杂的例子：银行账户模拟 {#更复杂的例子-银行账户模拟}

```lisp
(defun create-account (initial-balance)
  "创建一个银行账户闭包"
  (let ((balance initial-balance))
    (lambda (operation &optional amount)
      (case operation
        (:deposit  (incf balance amount)
                  (format t "存款 ~a, 余额: ~a~%" amount balance))
        (:withdraw (if (>= balance amount)
                       (progn (decf balance amount)
                              (format t "取款 ~a, 余额: ~a~%" amount balance))
                       (format t "余额不足! 当前余额: ~a~%" balance)))
        (:balance  (format t "当前余额: ~a~%" balance))
        (otherwise (format t "未知操作~%"))))))

;; 使用银行账户闭包
(let ((my-account (create-account 1000)))
  (funcall my-account :balance)     ; 当前余额: 1000
  (funcall my-account :deposit 500) ; 存款 500, 余额: 1500
  (funcall my-account :withdraw 200) ; 取款 200, 余额: 1300
  (funcall my-account :withdraw 2000) ; 余额不足! 当前余额: 1300
  )
```


## 闭包与普通函数的区别 {#闭包与普通函数的区别}

```lisp
;; 普通函数 - 无法保持状态
(defun ordinary-counter ()
  (let ((count 0))
    (incf count)))  ; 每次调用都会重新初始化count

;; 测试普通函数
(format t "普通函数第一次: ~a~%" (ordinary-counter))  ; 总是 1
(format t "普通函数第二次: ~a~%" (ordinary-counter))  ; 总是 1

;; 闭包函数 - 可以保持状态
(let ((closure-counter (make-counter 0)))
  (format t "闭包第一次: ~a~%" (funcall closure-counter))  ; 1
  (format t "闭包第二次: ~a~%" (funcall closure-counter))) ; 2
```

```text
普通函数第一次: 1
普通函数第二次: 1
闭包第一次: 1
闭包第二次: 2
```


## 实用示例：配置管理器 {#实用示例-配置管理器}

```lisp
(defun create-config-manager (default-settings)
  "创建一个配置管理器闭包"
  (let ((settings (copy-list default-settings)))  ; 复制默认设置
    (lambda (operation &optional key value)
      (case operation
        (:get (cdr (assoc key settings)))
        (:set (setf (cdr (assoc key settings)) value)
         t)
        (:list settings)
        (:reset (setf settings (copy-list default-settings))
         t)))))

;; 使用配置管理器
(let ((config (create-config-manager '((:theme . "dark")
                                       (:language . "en")
                                       (:notifications . t)))))
  (format t "当前主题: ~a~%" (funcall config :get :theme))
  (funcall config :set :theme "light")
  (format t "修改后主题: ~a~%" (funcall config :get :theme))
  (format t "所有设置: ~a~%" (funcall config :list)))
```


## 闭包的关键特性总结 {#闭包的关键特性总结}

-   状态保持：闭包可以记住并修改其创建时的环境
-   数据封装：外部无法直接访问闭包内部的变量
-   独立性：每个闭包实例都有自己独立的状态
-   词法作用域：闭包捕获的是变量在创建时的绑定，而不是值

<!--listend-->

```lisp
;; 演示多个独立闭包
(let ((counter1 (make-counter 0))
      (counter2 (make-counter 100)))
  (format t "Counter1: ~a, Counter2: ~a~%"
          (funcall counter1) (funcall counter2))  ; 1, 101
  (format t "Counter1: ~a, Counter2: ~a~%"
          (funcall counter1) (funcall counter2))) ; 2, 102

```
