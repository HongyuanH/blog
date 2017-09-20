---
layout:            post
title:             "SCons Tutorial Part 1 -- Installation & HelloWorld"
date:              2017-09-13
tags:              SCons
category:          SCons
author:            hongyuan

---

## Prerequisites

 * GCC/G++
 * Python

## Install SCons

```bash
sudo apt-get update
sudo apt-get install scons
```

## Basics

**SCons** is a software construction tool (build tool, or make tool) implemented in Python, which uses Python scripts as "configuration files" for software builds. A quick comparison between **SCons** and **Make** is shown below:

| Make | SCons |
|--------|--------|
| config file `Makefile` | config file `Sconstruct` |
| command `make` | command `scons` |
| command `make clean` | command `scons -c` |




## Hello World

Say we have a single CPP file:

<div class="div-nm">HelloWorld.cpp:</div>
```cpp
#include <iostream>

int main(){
	std::cout << "Hello World!" << std::endl;
	return 0;
}
```

Write a `Sconstruct` file in the same directory:

<div class="div-nm">Sconstruct:</div>
```python
Program("HelloWorld.cpp")
```

To compile the program, cd into the directory and issue `scons`:

```bash
$ cd HelloWorld
$ ls
HelloWorld.cpp  Sconstruct*
$ scons
scons: Reading SConscript files ...
scons: done reading SConscript files.
scons: Building targets ...
g++ -o HelloWorld.o -c HelloWorld.cpp
g++ -o HelloWorld HelloWorld.o
scons: done building targets.
$ ls
HelloWorld  HelloWorld.cpp  HelloWorld.o  Sconstruct
$ ./HelloWorld
Hello World!
```

The `Sconstruct` file is in essence a Python script (without the **.py** extension though). In this example, there's only one line in the `Sconstruct` file: it tells SCons to build a program from the file `HelloWorld.cpp`.

This is done by invoking the `Program()` method with the file name as parameter.

By default, if only one source file is given, the program will have the same name as the source file. To generate a program with a different name, add another parameter at the beginning of call to `Program()`:

<div class="div-nm">Sconstruct:</div>
```python
Program("MyHelloWorld", "HelloWorld.cpp")
```

Or with keyword parameters:

<div class="div-nm">Sconstruct:</div>
```python
Program(target="MyHelloWorld", source="HelloWorld.cpp")
```

To compile and run:

```bash
$ cd HelloWorld
$ ls
HelloWorld.cpp  Sconstruct*
$ scons
scons: Reading SConscript files ...
scons: done reading SConscript files.
scons: Building targets ...
g++ -o HelloWorld.o -c HelloWorld.cpp
g++ -o MyHelloWorld HelloWorld.o
scons: done building targets.
$ ls
HelloWorld.cpp  HelloWorld.o  MyHelloWorld  Sconstruct
$ ./MyHelloWorld
Hello World!
```

To clean the built files, issue `scons -c`:

```bash
$ scons -c
scons: Reading SConscript files ...
scons: done reading SConscript files.
scons: Cleaning targets ...
Removed HelloWorld.o
Removed MyHelloWorld
scons: done cleaning targets.
$ ls
HelloWorld.cpp  Sconstruct
```

## Links
* [Download the Examples](https://github.com/HongyuanH/scons_tutorial)
* [SCons Tutorial Part 1 -- Installation & HelloWorld]({{ site.github.url }}/blog/scons/SCons_Tutorial_Part1)
* [SCons Tutorial Part 2 -- Environment]({{ site.github.url }}/blog/scons/SCons_Tutorial_Part2)
* [SCons Tutorial Part 3 -- SConscript]({{ site.github.url }}/blog/scons/SCons_Tutorial_Part3)
* [SCons Tutorial Part 4 -- variant\_dir]({{ site.github.url }}/blog/scons/SCons_Tutorial_Part4)
* [SCons Tutorial Part 5 -- Object & Library]({{ site.github.url }}/blog/scons/SCons_Tutorial_Part5)
* [SCons Tutorial Part 6 -- Glob & filter]({{ site.github.url }}/blog/scons/SCons_Tutorial_Part6)
* [SCons Tutorial Part 7 -- Adding Command Line Options]({{ site.github.url }}/blog/scons/SCons_Tutorial_Part7)
* [SCons Tutorial Part 8 -- LINKCOM]({{ site.github.url }}/blog/scons/SCons_Tutorial_Part8)







