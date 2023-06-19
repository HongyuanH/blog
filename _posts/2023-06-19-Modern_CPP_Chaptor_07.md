---
layout:            post
title:             "Modern C++ Tutorials #7: Parallelism and Concurrency"
date:              2023-06-19
tags:              CPP
category:          CPP
author:            hongyuan

---

## 07 Parallelism and Concurrency

### std::mutex

TODO

### std::future

`std::packaged_task` is used when you have a callable that needs to be executed asynchronously and you want to obtain its result later.

```cpp
packaged_task<int()> task( [](){
    this_thread::sleep_for(chrono::seconds(1)); 
    return 7;
} );
auto f = task.get_future();
task();
cout << "You can see this after 1 second\n";
cout << f.get() << "\n";
```

`std::async` is a function template that creates and runs a `std::packaged_task` asynchronously. In the end a `std::packaged_task` is just a lower level feature for implementing `std::async`.

```cpp
auto f = async( launch::async, [](){
    this_thread::sleep_for(chrono::seconds(1)); 
    return 7;
} );
cout << "You can see this immediately!\n";
cout << f.get() << "\n";
cout << "This will be shown after a second!\n";
```

Note that `std::async`'s returned future has a special shared state, which demands that `future::~future()` blocks:

```cpp
auto test_async() {
    auto f = async( launch::async, [](){
        this_thread::sleep_for(chrono::seconds(1)); 
        return 7;
    } );
    // future::~future() will block if f is not captured
    return f;
}

int main() {
    test_async();
    cout << "This will be shown after a second!\n";

    auto f = test_async();
    cout << "You can see this immediately after a second!\n";
    cout << f.get() << "\n";
    cout << "This will be shown after a second!\n";
    return 0;
}
```

`std::promise` is a class template that allows a value or an exception to be shared between threads.

```cpp
promise<int> p;
auto f = p.get_future();
thread( [&p]{ 
    this_thread::sleep_for(chrono::seconds(1)); 
    p.set_value_at_thread_exit(9);
} ).detach();

cout << f.get() << "\n";
cout << "This will be shown after a second!\n";
```

