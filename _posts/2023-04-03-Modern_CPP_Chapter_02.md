---
layout:            post
title:             "Modern C++ Tutorials #2: Language Usability"
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

### Range-based for loop

This is not from the book:

```cpp
// This doesn't compile because vector<bool> is a specialized version
// The iterator returns a temporary bool object, which can't be bound to an lvalue ref 
std::vector<bool> v(10);
for (auto& e : v)
    e = true;

// This is OK because we can convert a temporary object to a rvalue ref
// Note that auto&& is universal reference and it doesn't mean e has to be an rvalue ref
std::vector<bool> v(10);
for (auto&& e : v)
    e = true;

// This is also OK because vector<int> doesn't return a temporary object
vector<int> v(10);
for (int& e : v)
    e = 1;
    
// Fun fact: this actually update the values of v to all true and assert pass!
std::vector<bool> v(10);
for (auto e : v)
    e = true;
for (auto e : v)
    assert(e);
```

From https://stackoverflow.com/a/25194424:

> std::vector<bool> returns a temporary proxy object when the iterators are dereferenced. That means that you have to use either auto, auto&& or const auto& but not auto& because you can't bind a temporary value to a non-const l-value reference.

From https://stackoverflow.com/a/13130795:

> The only advantage I can see is when the sequence iterator returns a proxy reference and you need to operate on that reference in a non-const way.

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

### Variadic templates

Recursion is a very easy way to think of and the most classic approach.

```cpp
// Recursive template
template<typename T0>
void printf1(T0 value) {
    cout << value << endl;
}
template<typename T, typename... Ts>
void printf1(T value, Ts... args) {
    cout << value << ' ';
    printf1(args...);
}

// Variable parameter template expansion
template<typename T, typename... Ts>
void printf2(T value, Ts... args) {
    cout << value << ' ';
    if constexpr (sizeof...(args) > 0)
        printf2(args...);
    else
        cout << endl;
}

// Initialize list expansion
template<typename... Ts>
void printf3(Ts... args) {
    // Expands to {0, ((cout << arg1 << ' '), 0), ((cout << arg2 << ' '), 0), ...}
    int unused[] = {0, ((cout << args << ' '), 0)...};
    cout << endl;
}
```

### Fold expression

```cpp
template<typename... Ts>
void printf4(Ts&&... args)
{
    // Expands to ((cout << arg1 << ' '), (cout << arg2 << ' '), ... << endl;
    ( (cout << args << ' '), ... ) << endl;
}

template <typename... Args>
void rPrintf(Args&& ...args) {
   int dum = 0;
   // Expands to (... = (cout << arg2 << ' ', dum) = (cout << arg1 << ' ', dum))
   (... = (cout << args << ' ', dum));
   cout << endl;
}
```

### Explicit delete default function

```cpp
class A{};

class B {
public:
    B(int b) {};
};

class C {
public:
    C() = default;
    C(int c) {};
    C(const C& c) = delete;
};

int main(){
    A a1;
    A a2(a1);

    // Not allowed: `B b;`
    B b1(0);
    B b2(b1);

    C c1;
    C c2(0);
    // Not allowed: `C c3(c2);`
    return 0;
}
```

