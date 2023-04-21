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

### decltype(auto)

Return type forwarding:

```cpp
template<class Fun, class... Args>
decltype(auto) wrap(Fun fun, Args&&... args) 
{ 
    return fun(forward<Args>(args)...); 
}

int retVal(int x) {
    return x;
}

int& retRef(int& x) {
    return x;
}

int main()
{
    int x = 100;
    decltype(auto) x1 = wrap(retVal, x);
    decltype(auto) x2 = wrap(retRef, x);
    cout << is_same<decltype(x1), int&>::value << endl; // 0
    cout << is_same<decltype(x2), int&>::value << endl; // 1
    return 0;
}
```

### Type alias templates

Templates are used to generate types. In traditional C++, typedef can define a new name for the type, but there is no way to define a new name for the template. Because the template is not a type. C++11 uses `using` to solve this problem.

```cpp
template<typename T, typename U>
class MagicType {
public:
    T dark;
    U magic;
};

// not allowed
// template<typename T>
// typedef MagicType<std::vector<T>, std::string> FakeDarkMagic;

template<typename T>
using TrueDarkMagic = MagicType<std::vector<T>, std::string>;
```

It can also be used for function pointer:

```cpp
// Before C++11
// typedef int (*FuncType)(int);

using FuncType = int(*)(int);

int Run(FuncType func) {
    return func(1);
}

int main(){
    cout << Run( [](int a) -> int { return a + 1; } ) << endl;
    return 0;
}
```
