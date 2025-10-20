---
title: "Emacs 笔记"
author: ["ubuntu"]
date: 2025-10-15T10:53:00+08:00
draft: false
---

## 基本快捷键 {#基本快捷键}

查看某个快捷键的功能：按下 `C-h k` 然后输入需要查询的 `快捷键` 。

sdf

{{< figure src="/ox-hugo/The-Android-software-stack.png" >}}


## 图片类 {#图片类}

-   插入图片：
    ```emacs-lisp
    [[/home/ubuntu/Pictures/The-Android-software-stack.png]]
    ```

<!--listend-->

-   怎么显示图片： `C-c C-x C-v`

<!--listend-->

-   控制图片大小：
    ```emacs-lisp
    # 将在 Emacs 中显示为 400 像素宽
    #+ATTR_ORG: :width 400
    [[/home/ubuntu/Pictures/The-Android-software-stack.png]]
    ```


## 文件管理 {#文件管理}

-   Dired: C-x d
    -   按下 g ：刷新缓冲区


## 解决问题 {#解决问题}


### 如何让flycheck和lsp-mode支持 C++17 {#如何让flycheck和lsp-mode支持-c-plus-plus-17}

-   配置 flycheck 支持 C++17
    -   flycheck 默认使用lsp-mode来进行错误检查
    -   在 flycheck 的 `use-package` 中 `:config` 中加入以下代码：
        ```emacs-lisp
        (use-package flycheck
          :ensure t
          :hook (prog-mode . flycheck-mode)
          :config
          (with-eval-after-load 'flycheck
            ;; 禁用 LSP 检查器（让 c/c++-clang 或 c/c++-gcc 成为首选）
            (setq-default flycheck-disabled-checkers '(lsp))
            ;; 指定 C++ 标准
            (setq flycheck-gcc-language-standard "c++17")
            (setq flycheck-clang-language-standard "c++17")))
        ```

<!--listend-->

-   配置 lsp-mode 支持 C++ 17
    -   lsp-mode 后端如果是 `clangd` 。
    -   在项目的根目录，创建 `.clangd` 文件，具体内容如下：
        ```emacs-lisp
        CompileFlags:
          Add: [-std=c++17]
        ```


### 终极办法 {#终极办法}

当遇到一个无法解决的问题的时候，可以临时创建一个干净的配置文件，例如：test-init.el。

-   裸启动 Emacs:
    -   cd "test-init.el所在的路径"
    -   `emacs -q -l test-init.el`
    -   这样就不会默认加载 `.emacs.d/下的配置文件` ，便于排查问题

<!--listend-->

-   使用国内镜像源：
    ```emacs-lisp
    ;; 初始化包管理
    (require 'package)

    ;; 设置镜像源
    (setq package-archives '(("gnu"   . "http://mirrors.tuna.tsinghua.edu.cn/elpa/gnu/")
                             ("melpa" . "http://mirrors.tuna.tsinghua.edu.cn/elpa/melpa/")
                             ("nongnu" . "http://mirrors.tuna.tsinghua.edu.cn/elpa/nongnu/") ; 如果需要非GNU软件包
                             ))

    ;; 初始化包系统
    (package-initialize)
    ```

<!--listend-->

-   尝试重命名 `/.emacs.d/elpa` ，重启Emacs重新加载插件
    -   底部minibuf应该会显示插件的加载信息
