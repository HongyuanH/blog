---
layout:            post
title:             "Modern C++ Tutorials #4: Containers"
date:              2023-06-04
tags:              CPP
category:          CPP
author:            hongyuan

---

## 04 Containers

### std::array

With a std::array, the element type and array length are part of the type information.

```cpp
// Doesn't work:
// void printArray(const std::array& myArray);
// OK but limited use case:
// void printArray(const std::array<int, 5>& myArray);

template <typename T, std::size_t size>
void printArray(const std::array<T, size>& myArray)
{
    for (auto element : myArray)
        std::cout << element << ' ';
    std::cout << '\n';
}
```
