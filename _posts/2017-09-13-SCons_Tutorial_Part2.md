---
layout:            post
title:             "SCons Tutorial Part 2 -- MiniProject"
date:              2017-09-13
tags:              SCons
category:          SCons
author:            hongyuan

---

## Mini Project

Say we have a mini project with the below layout:

![MiniProject_Layout.png]({{ site.github.url }}/res/2017-09-SCons_Tutorial/MiniProject_Layout.png#left)
<div style="clear:left"></div>

<div class="div-nm">HelloWorld.hpp:</div>
```cpp{:.line-numbers}
void HelloWorld();
```

<div class="div-nm">HelloWorld.cpp:</div>
```cpp{:.line-numbers}
#include <iostream>

void HelloWorld(){
	std::cout << "Hello World!" << std::endl;
}
```

<div class="div-nm">main.cpp:</div>
```cpp{:.line-numbers}
#include "HelloWorld.hpp"

int main(){
	HelloWorld();
	return 0;
}
```

Write a `Sconstruct` file in the root directory:

<div class="div-nm">Sconstruct:</div>
```python{:.line-numbers}
env = Environment()
env["CPPPATH"] = ["#/include"]
env.Program("MiniProject", ["src/HelloWorld.cpp","src/main.cpp"])
```

New things in the `Sconstruct` file:

1. Construction Environments [1]  
It is rare that all of the software in a large, complicated system needs to be built the same way. For example, different executable programs need to be linked with different libraries. SCons accommodates these different build requirements by allowing you to create and configure multiple **construction environments** that control how the software is built. A **construction environment** is an object that has a number of associated construction variables, each with a name and a value. Use the method `Environment()` to create a default build configuration. Check [the user guide](http://scons.org/doc/production/HTML/scons-user/ch07.html) for more details about **construction environment**.  
2. CPPPATH  
The `env` object is very much like a [Python Dictionary](https://www.tutorialspoint.com/python/python_dictionary.htm) which allows inserting/accessing elements via `[]`. Now that an environment has been created, assigning `["#/include"]` to `env["CPPPATH"]` will allow SCons to search for header files in the **include** folder (hash means root directory).   
3. env.Program()  
Instead of calling `Program()` directly, this time we use the environment object `env.Program()` to invoke the method, only in this way will `env["CPPPATH"]` take effect.  
4. Multiple source files as a [Python List](https://www.tutorialspoint.com/python/python_lists.htm)  
Multiple source files are passed to `env.Program()` in one go as a Python List. Same rule applies for assigning value to `env["CPPPATH"]`.

Compile and run:

```bash
$ cd MiniProject
$ ls
include  Sconstruct  src
$ scons
scons: Reading SConscript files ...
scons: done reading SConscript files.
scons: Building targets ...
g++ -o src/HelloWorld.o -c -Iinclude src/HelloWorld.cpp
g++ -o src/main.o -c -Iinclude src/main.cpp
g++ -o MiniProject src/HelloWorld.o src/main.o
scons: done building targets.
$ ls
include  MiniProject  Sconstruct  src
$ ./MiniProject
Hello World!
```

## References
[1] Construction Environments ([http://www.scons.org/doc/0.97/HTML/scons-user/c1051.html](http://www.scons.org/doc/0.97/HTML/scons-user/c1051.html))
[2] SCons User Guide, Chapter 7. Environments ([http://scons.org/doc/production/HTML/scons-user/ch07.html](http://scons.org/doc/production/HTML/scons-user/ch07.html))