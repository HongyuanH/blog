---
layout:            post
title:             "Modern C++ Tutorials, Notes #2"
date:              2023-04-03
tags:              CPP
category:          Modern CPP
author:            hongyuan

---

## 02 Language Usability

### nullptr

```cpp
void foo(char *);
void foo(int);

int main() {
    if (std::is_same<decltype(NULL), decltype(0)>::value)
        std::cout << "NULL == 0" << std::endl;
    if (std::is_same<decltype(NULL), decltype((void*)0)>::value)
        std::cout << "NULL == (void *)0" << std::endl;
    if (std::is_same<decltype(NULL), std::nullptr_t>::value)
        std::cout << "NULL == nullptr" << std::endl;

    foo(0);          // will call foo(int)
    // foo(NULL);    // doesn't compile
    foo(nullptr);    // will call foo(char*)
    return 0;
}
```
