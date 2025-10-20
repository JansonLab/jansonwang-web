---
title: "penetration Testing"
author: ["ubuntu"]
date: 2025-10-18T21:31:00+08:00
draft: false
---

## 压缩文件破解 {#压缩文件破解}


### 创建加密压缩文件(zip) {#创建加密压缩文件--zip}

```shell
# 加密单个文件
zip -e protected.zip file.txt

# 加密整个文件夹 (-r)
zip -er protected.zip my_folder/
```


### 解密 {#解密}

-   正常解密
    ```shell
    unzip protected.zip
    ```

-   暴力破解
    -   fcrackzip
    -   安装：
        ```shell
        sudo apt-get update
        sudo apt-get install fcrackzip
        ```

    -   开始破解：
        -   命令行选项：
            -   -b 指定暴力破解模式。
            -   -c 定义字符集 (\`a\`=小写字母, \`A\`=大写字母, \`1\`=数字, \`!\`=特殊符号)。
            -   -l 指定密码长度范围。
            -   -u 尝试解压文件来验证密码是否正确（更可靠）。

        -   示例：
            -   破解1到6位纯数字密码：
                ```shell
                fcrackzip -b -c '1' -l 1-6 -u protected.zip
                ```

            -   破解1到4位小写字母密码
                ```shell
                fcrackzip -b -c 'a' -l 1-4 -u protected.zip
                ```

            -   破解1到4位，包含大小写字母和数字的密码\*\*
                ```shell
                fcrackzip -b -c 'aA1' -l 1-4 -u protected.zip
                ```


### 工具实现 {#工具实现}

[暴力破解zip加密文件工具的设计与实现]({{< relref "暴力破解zip加密文件工具的设计与实现.md" >}})
