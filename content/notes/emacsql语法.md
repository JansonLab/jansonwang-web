---
title: "emacsql语法"
author: ["ubuntu"]
date: 2025-10-23T14:44:00+08:00
draft: false
---

`emacsql` 是一个让 Emacs Lisp 能用 Lisp 风格写 SQL 语句的数据库访问库。

-   它是 “SQL for Emacs Lisp”；
-   底层仍然是 SQLite（或其他数据库）；
-   上层用 Lisp 语法写查询，而不是传统的 SQL 字符串。


## 举例说明 {#举例说明}

传统 SQL（在其他语言中你会写）：

```sql
SELECT id, title FROM nodes WHERE title LIKE "%mutable%";
```

在 Emacs Lisp + emacsql 中，这样写：

```emacs-lisp
(emacsql db
  [:select [id title]
   :from nodes
   :where (like title "%mutable%")])
```


## 工作原理 {#工作原理}

1.  emacsql 本身只是一个 Emacs Lisp 层的 SQL 前端。

2.  它可以用不同的后端，比如：
    -   emacsql-sqlite：通过 SQLite 访问数据库（最常见）
    -   emacsql-psql：访问 PostgreSQL（较少用）

3.  Org-roam 用的就是 emacsql-sqlite，数据库文件通常位于：
    ```shell
    ~/.emacs.d/org-roam.db
    ```


## 在 Org-roam 中的应用 {#在-org-roam-中的应用}

Org-roam 使用 emacsql 来存储笔记信息：

-   每个 org 文件的 ID、标题、路径
-   每个链接的 源与目标
-   每个节点的 tags、aliases、refs 等

当你运行：

```emacs-lisp
(org-roam-db-sync)
```

Org-roam 会：

1.  扫描所有 org 文件；
2.  抽取结构；
3.  通过 emacsql 写入 SQLite 数据库。

当你运行：

```emacs-lisp
(org-roam-db-query [:select [id title file] :from nodes])
```

它通过 emacsql 从数据库读取数据返回给 Emacs。


## 简单的 emacsql 使用例子 {#简单的-emacsql-使用例子}

假设你想自己建一个数据库：

```emacs-lisp
(require 'emacsql)
(require 'emacsql-sqlite)

;; 创建一个数据库文件
(setq my-db (emacsql-sqlite "/tmp/test.db"))

;; 创建表
(emacsql my-db [:create-table people
                ([(id integer :primary-key)
                  (name text)
                  (age integer)])])

;; 插入数据
(emacsql my-db [:insert :into people :values [1 "Alice" 25]])
(emacsql my-db [:insert :into people :values [2 "Bob" 30]])

;; 查询数据
(emacsql my-db [:select [name age] :from people :where (> age 20)])
;; => (("Alice" 25) ("Bob" 30))
```


## 总结 {#总结}

`emacsql库` 让Emacs Lisp 可以操作数据库，默认的后端是 `SQLite` 。
