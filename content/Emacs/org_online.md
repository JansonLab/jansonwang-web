---
title: "ORG-Online"
author: ["ubuntu"]
date: 2025-10-20T15:33:00+08:00
draft: false
---

[ORG MODE 操作手册]({{< relref "org_mode_操作手册.md" >}})


## ORG Online {#org-online}


### 目标 {#目标}

基于ORG Roam，实现多平台的，包括电脑，手机的日程查看的修改。


### ORG Roam 的结构分析 {#org-roam-的结构分析}


#### ORG 的原理 {#org-的原理}

Org Roam 的核心思想是：

> 每一条知识是一张独立的“卡片（note）”，通过链接（ `[[id:xxx]]` ）彼此连接，形成知识网络。

Org Roam 的工作原理：

1.  每一篇笔记都是一个普通的 `.org` 文件。
2.  每个笔记都有唯一的 ID（例如 `:ID: 20251020T160002` ），作为该笔记的全局标识；
3.  当你在另一篇笔记中通 `[[id:xxx]]` 链接引用时，Org Roam 会在数据库中建立连接；
4.  这些连接信息会被保存到一个 SQLite 数据库（通常是 `org-roam.db` ）；
5.  最终，通过命令（如 `org-roam-node-find` 、 `org-roam-buffer-toggle` ）可以查看链接、反向链接、关系网络等。
