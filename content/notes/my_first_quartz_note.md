+++
title = "My-First-quartz-note"
author = ["ubuntu"]
date = 2025-10-13T18:31:00+08:00
draft = false
+++

## My-First-quartz-note {#my-first-quartz-note}

```emacs-lisp
(use-package org-roam
  :ensure t  ; 确保 org-roam 包被安装
  :init
  ;; 设置存放笔记的根目录
  (setq org-roam-directory (file-truename "~/org-roam-notes"))
  :config
  ;; 启动数据库自动同步模式，这是推荐的方式
  (org-roam-db-autosync-mode)

  ;; (可选) 定义一些常用快捷键，`C-c n` 是社区常用前缀
  :bind (("C-c n l" . org-roam-buffer-toggle)
         ("C-c n f" . org-roam-node-find)
         ("C-c n i" . org-roam-node-insert)
         ("C-c n g" . org-roam-graph)
         ("C-c n d" . org-roam-dailies-capture-today)))

```
