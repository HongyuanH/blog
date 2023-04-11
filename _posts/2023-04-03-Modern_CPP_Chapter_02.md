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
    foo(0);          // will call foo(int)
    // foo(NULL);    // doesn't compile
    foo(nullptr);    // will call foo(char*)
    return 0;
}
```

### constexpr

* A constexpr specifier used in an object declaration or non-static member function (until C++14) implies const. 
* A constexpr specifier used in a function or static data member (since C++17) declaration implies inline.
* The definition of constexpr functions in C++ is such that the function is guaranteed to be able to produce a constant expression when called such that only constant expressions are used in the evaluation.
* When passing non-constant expressions to a constexpr you may not get a constant expression.
* Use `static constexpr` instead of `constexpr` as recommended in [C++ Weekly - Ep 312 - Stop Using `constexpr` (And Use This Instead!)](https://www.youtube.com/watch?v=4pKtPWcl1Go&ab_channel=C%E1%90%A9%E1%90%A9WeeklyWithJasonTurner) 

```cpp
constexpr auto get_arr() {
    std::array<int, 10> arr{};
    int i = 0;
    for (auto& val : arr) {
        val = i++; 
    }
    return arr;
}
    
int main() {
    const int* p{nullptr};
    {
        static constexpr auto arr = get_arr();
        // constexpr auto arr = get_arr(); // stack-use-after-scope
        p = &arr[5];
    }
    cout << *p << endl;
    return 0;
}
```
