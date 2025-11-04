---
title: "ORG MODE C++ Running"
author: ["ubuntu"]
date: 2025-11-03T21:44:00+08:00
draft: false
---

## 方法一 {#方法一}

-   使用命令 C-c C-v t 导出一个文本文件
-   将该文本文本作为一个程序的输入

<!--listend-->

```text
Jason Wang
Jun
```

```cpp
#include <fstream>
#include <iostream>
#include <string>
using namespace std;

int main() {
  ifstream fin("input.txt");
  string name;
  getline(fin, name);
  cout << "Hello, " << name << "!" << endl;
}
```

```cpp
#include <stdio.h>

int main(int argc, char *argv[]) {
    if (argc >= 3) {
        printf("Hello, %s! You are %s years old.\n", argv[1], argv[2]);
    } else {
        printf("Usage: %s <name> <age>\n", argv[0]);
        return 1;
    }
    return 0;
}
```

```cpp
#include <iostream>
#include <vector>

int main(int argc, char *argv[]) {
    std::vector<int> numbers;

    for (int i = 1; i < argc; i++) {
        numbers.push_back(std::atoi(argv[i]));
    }

    int sum = 0;
    for (int num : numbers) {
        sum += num;
    }

    std::cout << "Sum: " << sum << std::endl;
    std::cout << "Average: " << (double)sum / numbers.size() << std::endl;

    return 0;
}
```

```cpp
#include <stdio.h>

int main(int argc, char *argv[]) {
    if (argc == 3) {
        printf("Name: %s\n", argv[1]);
        printf("City: %s\n", argv[2]);
    }
    return 0;
}
```

```cpp
#include <stdio.h>

int main(int argc, char *argv[]) {
    if (argc != 3) {
        printf("Usage: %s <input_file> <output_file>\n", argv[0]);
        return 1;
    }

    FILE *input = fopen(argv[1], "r");
    FILE *output = fopen(argv[2], "w");

    if (!input || !output) {
        printf("Error opening files!\n");
        return 1;
    }

    char buffer[256];
    while (fgets(buffer, sizeof(buffer), input)) {
        fputs(buffer, output);
    }

    fclose(input);
    fclose(output);
    printf("File copied successfully!\n");

    return 0;
}
```

```cpp
#include <iostream>
#include <fstream>
#include <string>

int main() {
    std::ifstream file("input.txt");
    if (!file.is_open()) {
        std::cerr << "无法打开文件: " << "input.txt" << std::endl;
        return 1;
    }

    std::string line;
    while (std::getline(file, line)) {
        std::cout << line << std::endl;  // 处理输入文件的内容
    }
    file.close();
    return 0;
}
```

```cpp
#include <fstream>
#include <stdexcept>
#include <string>
#include <iostream>
#include <sstream>


class SafeFile {
private:
  std::ifstream file_;
  std::string filename_;
  bool is_open_;

public:
  explicit SafeFile(const std::string& filename, const std::string& mode = "r")
    :filename_(filename),is_open_(false){
    if(mode == "r"){
      file_.open(filename,std::ios::in | std::ios::out | std::ios::app);
    } else if(mode == "w"){
      file_.open(filename,std::ios::out | std::ios::trunc);
    } else if(mode == "rw"){
      file_.open(filename, std::ios::in | std::ios::out);
    } else {
      throw std::invalid_argument("不支持的模式：只支持 'r' 'w' 'rw'");
    }

    if(!file_.is_open()){
      throw std::runtime_error("无法打开文件："+filename + "模式："+mode);
    }

    is_open_ = true;
  }

  ~SafeFile(){
    if(is_open_){
      file_.close();
    }
  }

  // 禁止拷贝，防止资源共享问题
  SafeFile(const SafeFile&) = delete;
  SafeFile& operator=(const SafeFile&) = delete;

  // 移动支持
  SafeFile(SafeFile&& other) noexcept
    : file_(std::move(other.file_)),
      filename_(std::move(other.filename_)),
      is_open_(other.is_open_){
    other.is_open_ = false;
  }

  // 检查文件是否打开
  bool isOpen() const {return is_open_ && file_.is_open();}

  // 获取文件名
  const std::string& filename() const { return filename_;}

  //安全读取一行（使用 std::getline）



};

int main(){
  std::cout << "helo" << std::endl;
  return 0;
}
```
