---
title: "Org Roam 工作原理"
author: ["ubuntu"]
date: 2025-10-20T16:55:00+08:00
draft: false
---

Org Roam 的核心思想是：

> 每一条知识是一张独立的“卡片（note）”，通过链接（ `[[id:xxx]]` ）彼此连接，形成知识网络。

[ORG MODE 操作手册]({{< relref "org_mode_操作手册.md" >}})

[ChatGPT Org Roam](https://chatgpt.com/s/t_68f618cdc1c48191ad6260d1346d6717)


## Org Roam 的工作原理 {#org-roam-的工作原理}

1.  每一篇笔记都是一个普通的 `.org` 文件。
2.  每个笔记都有唯一的 ID（例如 `:ID: 20251020T160002` ），作为该笔记的全局标识；
3.  当你在另一篇笔记中通 `[[id:xxx]]` 链接引用时，Org Roam 会在数据库中建立连接；

    >
4.  这些连接信息会被保存到一个 SQLite 数据库（通常是 `org-roam.db` ）；
5.  最终，通过命令（如 `org-roam-node-find` 、 `org-roam-buffer-toggle` ）可以查看链接、反向链接、关系网络等。

这让 Org Roam 成为 Emacs 中的本地化知识图谱系统。


## Org Roam 的文件结构 {#org-roam-的文件结构}

假设Org Roam的根目录为 `~/org-roam/`

典型结构如下：

```shell
org-roam/
├── daily/
│   ├── 2025-10-20.org
│   └── 2025-10-19.org
├── people/
│   ├── Alan-Turing.org
│   ├── John-Von-Neumann.org
├── topics/
│   ├── computer-science.org
│   ├── emacs.org
│   └── org-roam.org
└── org-roam.db
```

说明：

-   `.org` 文件：每个文件是一条笔记；
-   `org-roam.db` ： SQLite 数据库，存储笔记之间的关系；
-   `daily/` ：存放“日记型”笔记（用 `org-roam-dailies` 管理）；
-   目录结构可以自定义，不影响 Org Roam 的索引；
-   每个笔记顶部通常会有类似以下的 文件头部信息：
    ```org
    :PROPERTIES:
    :ID:       20251020T160002
    :END:
    #+title: Org Roam 基础教程
    #+filetags: :emacs:org:
    ```


## Org Roam 的使用教程 {#org-roam-的使用教程}


### 安装 Org Roam {#安装-org-roam}

在 Emacs 配置文件（如 `init.el` ） 中添加：

```emacs-lisp
(use-package org-roam
:ensure t
:custom
(org-roam-directory (file-truename "~/org-roam/"))
(org-roam-db-location (expand-file-name "org-roam.db" org-roam-directory))
:bind (("C-c n l" . org-roam-buffer-toggle)  ;; 打开侧边栏显示链接
       ("C-c n f" . org-roam-node-find)      ;; 查找笔记
       ("C-c n i" . org-roam-node-insert)    ;; 插入笔记链接
       ("C-c n c" . org-roam-capture))       ;; 新建笔记
:config
(org-roam-db-autosync-mode))
```

> 作用：自动同步数据库；提供快速键绑定；设置笔记存放目录。


## Org Roam 的底层命令 {#org-roam-的底层命令}

Org-roam v2 使用一个 SQLite 数据库（默认位于 ~/.emacs.d/org-roam.db）来存储：

-   每篇笔记（node）的 ID、标题、文件路径
-   反向链接（backlinks）
-   标签（tags）、别名（aliases）等信息

执行以下命令:

```emacs-lisp
M-x org-roam-db-sync
```

它会重新扫描你的所有 org 文件，然后把这些信息写入数据库。


### 数据库查询 {#数据库查询}

org-roam-db-query 是一个底层 API，用于直接对这个数据库执行查询。它使用 emacsql 语法（类似 SQL，但用 Lisp 风格书写）。

> [emacsql语法]({{< relref "emacsql语法.md" >}})

使用以下命令查询数据库中是否有指定的ID

```emacs-lisp
(org-roam-db-query [:select [id title file] :from nodes :where (= id "8502ac2f-cb07-41b0-9ed0-14971b1d0fc7")])
```

|                                      |               |                                             |
|--------------------------------------|---------------|---------------------------------------------|
| 8502ac2f-cb07-41b0-9ed0-14971b1d0fc7 | mutable修饰成员变量 | /home/ubuntu/personalWiki/mutable修饰成员变量.org |
