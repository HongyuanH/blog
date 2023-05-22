---
layout:            post
title:             "Modern C++ Tutorials, Notes #3"
date:              2023-05-22
tags:              CPP
category:          Modern CPP
author:            hongyuan

---

## 03 Language Runtime Enhancement

### std::bind and std::placeholder

```cpp
int foo(int a, int b, int c) {
    return a * b + c;
}

int main() {
    auto bindFoo = bind(foo, placeholders::_1, 1, 2);
    cout << bindFoo(1) << endl;
}
```
