---
title: "mutable修饰成员变量"
author: ["Janson"]
date: 2026-05-13T17:08:00+08:00
draft: false
---

如果某个成员变量在逻辑上不属于对象状态的一部分（例如，用于缓存、访问计数或线程同步），但需要在const成员函数中被修改，可以用 mutable关键字修饰该成员变量

```cpp
class CachedData {
private:
  mutable bool cacheValid; // 被mutable修饰
  mutable int cachedValue;

public:
  int getValue() const { // const成员函数
    if (!cacheValid) {
      // 允许修改mutable成员
      cachedValue = computeValue();
      cacheValid = true;
    }
    return cachedValue;
  }
};
```
