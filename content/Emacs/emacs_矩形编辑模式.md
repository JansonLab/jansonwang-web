---
title: "Emacs 矩形编辑模式"
author: ["ubuntu"]
date: 2025-11-08T12:49:00+08:00
draft: false
---

[Emacs 矩形编辑](https://yuanbao.tencent.com/bot/app/share/chat/VMgAjNVpUB1q)


## 矩形编辑模式 {#矩形编辑模式}

将选定的块同时向右移动两个空格的宽度：

1.  标记矩形区域
    C-x SPC

2.  在矩形区域前插入两个空格
    -   C-x r t
    -   然后输出两个空格

3.  或者缩进整个矩形区域
    -   C-x TAB
    -   然后通过左右方向键来控制缩进

4.  删除一列或多列
    -   C-x SPA
    -   C-f 移动光标选中
    -   C-x r d

5.  清空为空格
    -   C-x SPA
    -   C-f 移动光标选中
    -   C-x r c

> 剪切矩形 C-x r k
