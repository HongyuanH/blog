---
layout:            post
title:             "Modern C++ Tutorials #7: Parallelism and Concurrency"
date:              2023-06-19
tags:              CPP
category:          CPP
author:            hongyuan

---

## 07 Parallelism and Concurrency

### mutex and lock

* `std::lock_guard`: RAII lock
* `std::unique_lock`: similar to `std::lock_guard` with support for manual locking and unlocking, also used for locking `std::shared_mutex` when writing
* `std::shared_lock`: provides concurrent reading of shared resource, must be used on a `std::shared_mutex` object

### std::future

An asynchronous operation (created via `std::async`, `std::packaged_task`, or `std::promise`) can provide a `std::future` object to the creator of that asynchronous operation.

`std::packaged_task` is used when you have a callable that needs to be executed asynchronously and you want to obtain its result later.

```cpp
packaged_task<int()> task( [](){
    this_thread::sleep_for(chrono::seconds(1)); 
    return 7;
} );
auto f = task.get_future();
thread worker(move(task));
worker.join();
cout << "You can see this immediately!\n";
f.get(); //block
cout << "You can see this after a second\n";
```

`std::async` is just a function template that creates and runs a `std::packaged_task` asynchronously.
Think of it as an enhancement to `std::thread` which allows you to get the result back from another thread.

```cpp
auto f = async( launch::async, [](){
    this_thread::sleep_for(chrono::seconds(1)); 
    return 7;
} );
cout << "You can see this immediately!\n";
f.get(); //block
cout << "This will be shown after a second!\n";
```

Note that `std::async`'s returned future will block upon destruction:

```cpp
auto test_async() {
    auto f = async( launch::async, [](){
        this_thread::sleep_for(chrono::seconds(1)); 
        return 7;
    } );
    // future::~future() will block
    return f;
}

int main() {
    test_async(); //block
    cout << "This will be shown after a second!\n";

    auto f = test_async(); //non-block
    cout << "You can see this immediately !\n";
    f.get(); //block
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
f.get(); //block
cout << "This will be shown after a second!\n";
```

Relationship of `std::async`, `std::packaged_task` and `std::promise`? See [this StackOverflow answer](https://stackoverflow.com/a/12335206).

> * `std::async` is the most convenient and straight-forward way to perform an asynchronous com­pu­ta­tion is via the async function template, which returns the future. We have very little control over the details. In particular, we don't even know if the function is exe­cu­ted concurrently, serially upon get(), or by some other black magic.
> * `std::packaged_task` can implement something like `std::async`, but in a fashion that we control.
> * `std::promise` is the lowest level of the implementation. The principal steps are these:
>     * The calling thread makes a promise.
>     * The calling thread obtains a future from the promise.
>     * The promise, along with function arguments, are moved into a separate thread.
>     * The new thread executes the function and fulfills the promise.
>     * The original thread retrieves the result.
