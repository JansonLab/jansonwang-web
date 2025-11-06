---
title: "C++ IO库继承链"
author: ["ubuntu"]
date: 2025-11-06T11:20:00+08:00
draft: false
---

C++ 使用标准库类来处理面向流的输入和输出：

-   iostream 处理控制台 IO
-   fstream 处理命名文件 IO
-   stringstream 完成内存 string 的IO

<!--listend-->

```text
ios_base
 └── basic_ios
      ├── basic_istream
      │    ├── istream
      │    │    └── ifstream / istringstream
      │    └── wistream
      ├── basic_ostream
      │    ├── ostream
      │    │    └── ofstream / ostringstream
      │    └── wostream
      └── basic_iostream
           ├── iostream
           │    └── fstream / stringstream
           └── wiostream
```

```plantuml
@startuml
skinparam backgroundColor #FFFFFF
skinparam class {
    BorderColor black
    BackgroundColor white
    ArrowColor black
    FontSize 13
}
title C++ IO 库类继承关系

' 基本流缓冲区类
class basic_streambuf<CharT, Traits> {
    + pubimbue()
    + getloc()
    + pubsetbuf()
    + pubseekoff()
    + pubseekpos()
    + pubsync()
}

' 字符特性类
class char_traits<CharT>

' 基本流类
class basic_ios<CharT, Traits> {
    # basic_streambuf<CharT, Traits>* sbuf
    + good()
    + eof()
    + fail()
    + bad()
    + clear()
    + rdstate()
}

' 输入流类
class basic_istream<CharT, Traits> {
    + operator>>()
    + get()
    + getline()
    + read()
    + tellg()
    + seekg()
}

' 输出流类
class basic_ostream<CharT, Traits> {
    + operator<<()
    + put()
    + write()
    + tellp()
    + seekp()
    + flush()
}

' 输入输出流类
class basic_iostream<CharT, Traits>

' 文件流类
class basic_ifstream<CharT, Traits>
class basic_ofstream<CharT, Traits>
class basic_fstream<CharT, Traits>

' 字符串流类
class basic_istringstream<CharT, Traits>
class basic_ostringstream<CharT, Traits>
class basic_stringstream<CharT, Traits>

' 标准流对象
object cin
object cout
object cerr
object clog

' 继承关系
basic_streambuf <|-- basic_ios
basic_ios <|-- basic_istream
basic_ios <|-- basic_ostream
basic_istream <|-- basic_iostream
basic_ostream <|-- basic_iostream

' 文件流继承
basic_istream <|-- basic_ifstream
basic_ostream <|-- basic_ofstream
basic_iostream <|-- basic_fstream

' 字符串流继承
basic_istream <|-- basic_istringstream
basic_ostream <|-- basic_ostringstream
basic_iostream <|-- basic_stringstream

' 标准流实例化关系
basic_istream <.. cin : 实例化
basic_ostream <.. cout : 实例化
basic_ostream <.. cerr : 实例化
basic_ostream <.. clog : 实例化

' 模板参数依赖
char_traits <.. basic_streambuf : Traits参数
char_traits <.. basic_ios : Traits参数
char_traits <.. basic_istream : Traits参数
char_traits <.. basic_ostream : Traits参数

note right of basic_streambuf
<b>流缓冲区基类</b>\n
负责实际的I/O操作
end note

note left of basic_ios
<b>基础IO类</b>\n
管理流状态和缓冲区
end note

note bottom of basic_istream
<b>输入流基类</b>\n
提供格式化输入操作
end note

@enduml
```

{{< figure src="/ox-hugo/cpp-io-inheritance.png" >}}

```plantuml
@startuml
skinparam backgroundColor #F9F9F9
skinparam class {
    BorderColor #333333
    BackgroundColor #FFFFFF
    ArrowColor #0066CC
    FontName Arial
    AttributeFontSize 10
    MethodFontSize 9
}
title C++ 标准IO库完整继承体系

package "标准流类型别名" {
  class istream
  class ostream
  class iostream
  class ifstream
  class ofstream
  class fstream
  class istringstream
  class ostringstream
  class stringstream

  note right
  这些是char类型的模板特化别名：
  typedef basic_istream<char> istream;
  typedef basic_ostream<char> ostream;
  typedef basic_iostream<char> iostream;
  end note
}

package "模板基类" {
  abstract class "basic_streambuf<CharT, Traits>" as streambuf {
    + in_avail(): streamsize
    + snextc(): int_type
    + sbumpc(): int_type
    + sgetc(): int_type
    + sgetn(char_type*, streamsize): streamsize
    + sputc(char_type): int_type
    + sputn(const char_type*, streamsize): streamsize
  }

  abstract class "basic_ios<CharT, Traits>" as ios {
    # streambuf_type* rdbuf()
    # void init(streambuf_type*)
    + char_type fill()
    + char_type fill(char_type)
    + iostate rdstate()
    + void clear(iostate)
    + bool good()
    + bool eof()
    + bool fail()
    + bool bad()
  }

  abstract class "basic_istream<CharT, Traits>" as istream_base {
    + operator>>(short&)
    + operator>>(int&)
    + operator>>(long&)
    + operator>>(float&)
    + operator>>(double&)
    + operator>>(bool&)
    + get(): int_type
    + get(char_type&)
    + getline(char_type*, streamsize)
    + read(char_type*, streamsize)
    + gcount(): streamsize
  }

  abstract class "basic_ostream<CharT, Traits>" as ostream_base {
    + operator<<(short)
    + operator<<(int)
    + operator<<(long)
    + operator<<(float)
    + operator<<(double)
    + operator<<(bool)
    + put(char_type)
    + write(const char_type*, streamsize)
    + flush()
  }

  abstract class "basic_iostream<CharT, Traits>" as iostream_base
}

package "文件流" {
  class "basic_filebuf<CharT, Traits>" as filebuf
  class "basic_ifstream<CharT, Traits>" as ifstream_base
  class "basic_ofstream<CharT, Traits>" as ofstream_base
  class "basic_fstream<CharT, Traits>" as fstream_base
}

package "字符串流" {
  class "basic_stringbuf<CharT, Traits>" as stringbuf
  class "basic_istringstream<CharT, Traits>" as istringstream_base
  class "basic_ostringstream<CharT, Traits>" as ostringstream_base
  class "basic_stringstream<CharT, Traits>" as stringstream_base
}

package "预定义标准流对象" {
  object cin
  object cout
  object cerr
  object clog
  object wcin
  object wcout
  object wcerr
  object wclog
}

' 继承关系
streambuf <|-- ios
ios <|-- istream_base
ios <|-- ostream_base
istream_base <|-- iostream_base
ostream_base <|-- iostream_base

' 文件流继承
streambuf <|-- filebuf
filebuf <|-- ifstream_base
filebuf <|-- ofstream_base
filebuf <|-- fstream_base
istream_base <|-- ifstream_base
ostream_base <|-- ofstream_base
iostream_base <|-- fstream_base

' 字符串流继承
streambuf <|-- stringbuf
stringbuf <|-- istringstream_base
stringbuf <|-- ostringstream_base
stringbuf <|-- stringstream_base
istream_base <|-- istringstream_base
ostream_base <|-- ostringstream_base
iostream_base <|-- stringstream_base

' 别名关系
istream_base <.. istream : typedef
ostream_base <.. ostream : typedef
iostream_base <.. iostream : typedef
ifstream_base <.. ifstream : typedef
ofstream_base <.. ofstream : typedef
fstream_base <.. fstream : typedef
istringstream_base <.. istringstream : typedef
ostringstream_base <.. ostringstream : typedef
stringstream_base <.. stringstream : typedef

' 标准流对象实例化
istream ..> cin : 全局对象
ostream ..> cout : 全局对象
ostream ..> cerr : 全局对象
ostream ..> clog : 全局对象

' 添加说明框
note as N1
<b>设计模式：</b>
C++ IO库使用模板和继承实现
- 模板提供类型安全
- 继承提供多态行为
- 流缓冲区模式分离I/O操作
end note

N1 .. streambuf

legend right
| 颜色说明 |
| --- |
| <color:#0066CC>蓝色箭头</color>：类继承 |
| <color:#00CC66>绿色箭头</color>：类型别名 |
| <color:#CC6600>橙色箭头</color>：对象实例 |
| 虚线：依赖关系 |
endlegend

@enduml
```

{{< figure src="/ox-hugo/cpp-io-detailed.png" >}}
