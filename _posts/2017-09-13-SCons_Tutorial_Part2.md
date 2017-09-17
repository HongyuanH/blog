---
layout:            post
title:             "SCons Tutorial Part 2 -- Environment"
date:              2017-09-13
tags:              SCons
category:          SCons
author:            hongyuan

---

## Construction Environments[1,2]

It is rare that all of the software in a large, complicated system needs to be built the same way. For example, different executable programs need to be linked with different libraries. SCons accommodates these different build requirements by allowing you to create and configure multiple **construction environments** that control how the software is built.

In our last example, we did not create any environment but built a program with a direct method of `Program()`:

```python
Program(target="MyHelloWorld", source="HelloWorld.cpp")
```

A more appropriate way of doing it is to first create a bulid environment with the method `Environment()` and then build the program under the environment:

```python
env = Environment()
env.Program(target="MyHelloWorld", source="HelloWorld.cpp")
```

Without giving any argument to `Environment()`, SCons creates a default environment. By default, SCons initializes every new construction environment with a set of construction variables based on the tools that it finds on your system, plus the default set of builder methods necessary for using those tools. The construction variables are initialized with values describing the C compiler, the Fortran compiler, the linker, etc., as well as the command lines to invoke them.

When you initialize a construction environment you can set the values of the environment's construction variables to control how a program is built. For example:

```python
env = Environment(CC="gcc", CCFLAGS=["-O0", "-g"])
```

The `env` object is very much like a [Python Dictionary](https://www.tutorialspoint.com/python/python_dictionary.htm) which allows inserting/deleting/accessing elements via the operator`[]` to update its construction variables:

```python
env = Environment()
env["CC"] = "gcc"
env["CCFLAGS"] = ["-O0"]
env["CCFLAGS"] += ["-g"]
```

Some common keywords to use with the environment are listed below:  

| Keyword | Function |
|--------|--------|
| CC | compiler to use |
| CPPPATH | header search path |
| CCFLAGS | compile-time flags |
| CPPDEFINES | preprocessor |
| LIBPATH | library search path |
| LIBS | libraries to link against |
| LINKFLAGS | link time flags |

It is also possible to create your own variable and pass it around with the environment object:

```python
env = Environment()
env["name"] = "MyHelloWorld"
env.Program(target=env["name"], source="HelloWorld.cpp")
```

You can check [the user guide](http://scons.org/doc/production/HTML/scons-user/ch07s02.html) for more details about **construction environment**.  

## Example

<div class="div-nm">Project Layout:</div>
```
Environment
|--include
|  |--HelloWorld.hpp
|--src
|  |--HelloWorld.cpp
|  |--main.cpp
|--Sconstruct
```

<div class="div-nm">HelloWorld.hpp:</div>
```cpp
void HelloWorld();
```

<div class="div-nm">HelloWorld.cpp:</div>
```cpp
#include <iostream>

void HelloWorld(){
	std::cout << "Hello World!" << std::endl;
}
```

<div class="div-nm">main.cpp:</div>
```cpp
#include "HelloWorld.hpp"

int main(){
	HelloWorld();
	return 0;
}
```

<div class="div-nm">Sconstruct:</div>
```python
env = Environment()
env["CPPPATH"] = ["#/include"]
env.Program("main", ["src/HelloWorld.cpp","src/main.cpp"])
```

New things in the `Sconstruct` file:

1. A default envrionment is created with `Environment()`
2. `["#/include"]` is assigned to `env["CPPPATH"]` which allows SCons to search for header files in the **include** folder (hash means root directory)
3. `env.Program()` is used instead of `Program()`, only in this way will `env["CPPPATH"]` take effect

Compile and run:

```bash
$ cd Environment
$ ls
include  Sconstruct  src
$ scons
scons: Reading SConscript files ...
scons: done reading SConscript files.
scons: Building targets ...
g++ -o src/HelloWorld.o -c -Iinclude src/HelloWorld.cpp
g++ -o src/main.o -c -Iinclude src/main.cpp
g++ -o main src/HelloWorld.o src/main.o
scons: done building targets.
$ ls
include  main  Sconstruct  src
$ ./main
Hello World!
```

## References
[1] Construction Environments ([http://www.scons.org/doc/0.97/HTML/scons-user/c1051.html](http://www.scons.org/doc/0.97/HTML/scons-user/c1051.html))  
[2] SCons User Guide, 7.2. Construction Environments ([http://scons.org/doc/production/HTML/scons-user/ch07s02.html](http://scons.org/doc/production/HTML/scons-user/ch07s02.html))