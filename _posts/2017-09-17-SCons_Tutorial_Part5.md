---
layout:            post
title:             "SCons Tutorial Part 5 -- Object & Library"
date:              2017-09-17
tags:              SCons
category:          SCons
author:            hongyuan

---

## Object & Library

In our previous examples, we created programs with the method `Program()`. How do we compile source files into objects and libriries? Check our below example!

## Example

<div class="div-nm">Project Layout:</div>
```
ObjAndLib
|--include
|  |--HelloWorld.hpp
|--src
|  |--HelloWorld.cpp
|--target
|  |--main.cpp
|--Sconstruct
```

<div class="div-nm">Sconstruct:</div>
```python{:.line-numbers}
# global env
env = Environment()
env["CPPPATH"] = ["#/include"]
# build library (dynamic and static)
env.Object(target="build/src/HelloWorld.o", source="src/HelloWorld.cpp")
env.Library(target="build/lib/libHelloWorld.so", source="build/src/HelloWorld.o")
env.StaticLibrary(target="build/lib/libHelloWorld.a", source="build/src/HelloWorld.o")
# build object file for target
env.Object(target="build/target/main.o", source="target/main.cpp")
# build target with dynamic library (.so)
env1 = env.Clone()
env1["LIBS"] = ["HelloWorld"]
env1["LIBPATH"] = ["build/lib"]
env1.Program(target="build/testLibrary", source="build/target/main.o")
# build target with static library (.a)
env2 = env.Clone()
env2["LIBS"] = []
env2["LIBPATH"] = []
env2.Program(target="build/testStaticLibrary", 
            source=["build/target/main.o", "build/lib/libHelloWorld.a"])
```

In this example, we did the following things:

 1. build an object file **HelloWorld.o** using the method `Object()` (line #5)
 2. build a dynamic library **libHelloWorld.so** from **HelloWorld.o** using the method `Library()` (line #6)
 3. build a static library **libHelloWorld.a** from **HelloWorld.o** using the method `StaticLibrary()` (line #7)
 4. buld an object file **main.o** using the method `Object()` (line #9)
 5. clone `env` to `env1` and update its `["LIBS"]` and `["LIBPATH"]` so that `env1` will build the target with the dynamic library (line #11 and #12)
 6. build the target **testLibrary** with `env1` (line #14)
 7. clone `env` to `env2` and update its `["LIBS"]` and `["LIBPATH"]` so that `env2` will NOT build the target with the dynamic library (line #17 and #18)
 8. biuld the target **testStaticLibrary** with `env2`, with the static library appended as one of the sources (line #19 and #20)

Compile result:

```bash{:.line-numbers}
scons
scons: Reading SConscript files ...
scons: done reading SConscript files.
scons: Building targets ...
g++ -o build/src/HelloWorld.o -c -Iinclude src/HelloWorld.cpp
ar rc build/lib/libHelloWorld.a build/src/HelloWorld.o
ranlib build/lib/libHelloWorld.a
ar rc build/lib/libHelloWorld.so build/src/HelloWorld.o
ranlib build/lib/libHelloWorld.so
g++ -o build/target/main.o -c -Iinclude target/main.cpp
g++ -o build/testLibrary build/target/main.o -Lbuild/lib -lHelloWorld
g++ -o build/testStaticLibrary build/target/main.o build/lib/libHelloWorld.a
scons: done building targets.
```

The usage of `Object()`, `Library()` and `StaticLibrary()` is quite straightforward. But why should we clone the environment into `env1` and `env2` instead of updating `env` itself? Let's try again with the below script:

```python{:.line-numbers}
# global env
env = Environment()
env["CPPPATH"] = ["#/include"]
# build library (dynamic and static)
env.Object(target="build/src/HelloWorld.o", source="src/HelloWorld.cpp")
env.Library(target="build/lib/libHelloWorld.so", source="build/src/HelloWorld.o")
env.StaticLibrary(target="build/lib/libHelloWorld.a", source="build/src/HelloWorld.o")
# build object file for target
env.Object(target="build/target/main.o", source="target/main.cpp")
# build target with dynamic library (.so)
env["LIBS"] = ["HelloWorld"]
env["LIBPATH"] = ["build/lib"]
env.Program(target="build/testLibrary", source="build/target/main.o")
# build target with static library (.a)
env["LIBS"] = []
env["LIBPATH"] = []
env.Program(target="build/testStaticLibrary", 
            source=["build/target/main.o", "build/lib/libHelloWorld.a"])
```

Compile result:

```bash{:.line-numbers}
scons
scons: Reading SConscript files ...
scons: done reading SConscript files.
scons: Building targets ...
g++ -o build/src/HelloWorld.o -c -Iinclude src/HelloWorld.cpp
ar rc build/lib/libHelloWorld.a build/src/HelloWorld.o
ranlib build/lib/libHelloWorld.a
ar rc build/lib/libHelloWorld.so build/src/HelloWorld.o
ranlib build/lib/libHelloWorld.so
g++ -o build/target/main.o -c -Iinclude target/main.cpp
g++ -o build/testLibrary build/target/main.o
build/target/main.o: In function `main':
main.cpp:(.text+0x5): undefined reference to `HelloWorld()'
collect2: error: ld returned 1 exit status
scons: *** [build/testLibrary] Error 1
scons: building terminated because of errors.
```

Oops, undefined reference to "HelloWorld()" when building target with dynamic library (line #13)? Let's compare the command issued by SCons in the two cases (line #11). With cloned environment:

```bash
g++ -o build/testLibrary build/target/main.o -Lbuild/lib -lHelloWorld
```

and without:

```bash
g++ -o build/testLibrary build/target/main.o
```

The reason behind this is that the environment variables are updated before the actual build is performed, even if `env["LIBS"] = []` and `env["LIBPATH"] = []` are plcaed after `env.Program(target="build/testLibrary", source="build/target/main.o")` in our script. So when the actual build takes place, the global envrionment LIBS and LIBPATH have been removed. To avoid this a clone of the environment is necessary for each build.

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