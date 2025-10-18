---
title: "ORG MODE 导出C++代码的方式"
author: ["ubuntu"]
date: 2025-10-15T16:10:00+08:00
draft: false
---

OEG MODE中标记C++代码块的方式有：小写c，大写C，小写cpp，大写CPP,还可以标记c++，大写C++,他们之间的区别是什么，我们可以以此尝试。


## 小写c {#小写c}

-   携带main函数
    -   提供的块参数： `c :tangle c.cpp`
        ```c
        int main() {
          int a = 100;
          int b = 200;
          int c = 100;
        }
        ```

    -   导出的结果：c.cpp的文件内容如下：
        ```c
        int main() {
          int a = 100;
          int b = 200;
          int c = 100;
        }
        ```
