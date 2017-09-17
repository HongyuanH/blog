---
layout:            post
title:             "SCons Tutorial Part 3 -- SConscript"
date:              2017-09-14
tags:              SCons SConscript
category:          SCons
author:            hongyuan

---

## SConscript[1]

The source code for large software projects rarely stays in a single directory, but is nearly always divided into a hierarchy of directories. Organizing a large software build using **SCons** involves creating a hierarchy of build scripts using the `SConscript()` function.

As we've already seen, the build script at the top of the tree is called **SConstruct**. The top-level SConstruct file can use the `SConscript()` function to include other subsidiary scripts in the build. These subsidiary scripts can, in turn, use the `SConscript()` function to include still other scripts in the build. By convention, these subsidiary scripts are usually named **SConscript**.

<span style="color:red">NOTE: the second character in both the method name `SConscript()` and the file name of **SConscript** is a capital **C**! This is a very common mistake because the second character in top-level script file name **Sconsctruct** is a lower case **c**!</span>

If there's a typo in the method name of `SConscript()`:

> NameError: name 'Sconscript' is not defined:

If there's a typo in the file name passed to `SConscript()`:

> scons: warning: Ignoring missing SConscript 'src/Sconscript'

If there's a typo in the file name of the SConscript:

> scons: warning: Ignoring missing SConscript 'src/SConscript'

## Example

<div class="div-nm">Project Layout:</div>
```
SConscript
|--src1
|  |--HelloWorld.cpp
|  |--SConscript
|--src2
|  |--testA
|  |  |--testA.cpp
|  |  |--SConscript
|  |--testB
|     |--testB.cpp
|     |--SConscript
|--Sconstruct
```

<div class="div-nm">Sconstruct:</div>
```python
env = Environment()
env["LIBS"] = ["pthread"]
SConscript("src1/SConscript", exports="env")
SConscript("src2/SConscript", exports="env")
```

<div class="div-nm">src1/SConscript:</div>
```python
Import("env")
env = env.Clone()
env.Program("HelloWorld.cpp")
```

<div class="div-nm">src2/SConscript:</div>
```python
Import("env")
env = env.Clone()
env["CCFLAGS"] = ["-std=c++11"]
SConscript("testA/SConscript", exports="env")
SConscript("testB/SConscript", exports="env")
```

<div class="div-nm">src2/testA/SConscript:</div>
```python
Import("env")
env = env.Clone()
env["CCFLAGS"] += ["-O1"]
env.Program("testA.cpp")
```

<div class="div-nm">src2/testB/SConscript:</div>
```python
Import("env")
env = env.Clone()
env["CCFLAGS"] += ["-O2"]
env.Program("testB.cpp")
```

* In the top-level **SConstruct** script, a global environment is created, it then calls the `SConscript()` method with `exports="env"` to pass the global environment into subsidiary **SConscript** files.
* **Sconscript** files in sub-directories `Import("env")` from parent scirpt, and use `env.Clone()` to make a local copy of the environment, avoiding changing the settings globally.

As a result:

 * all the three programs will be linked with **pthread**;
 * programs (testA and testB) in **src2** will be built with `-std=c++11`;
 * testA will be built with `-O1`
 * testB will be built with `-O2`

Compile and run:

```bash
$ cd SConscriptProject
$ ls
Sconstruct  src1  src2
$ scons
scons: Reading SConscript files ...
scons: done reading SConscript files.
scons: Building targets ...
g++ -o src1/HelloWorld.o -c src1/HelloWorld.cpp
g++ -o src1/HelloWorld src1/HelloWorld.o -lpthread
g++ -o src2/testA/testA.o -c -std=c++11 -O1 src2/testA/testA.cpp
g++ -o src2/testA/testA src2/testA/testA.o -lpthread
g++ -o src2/testB/testB.o -c -std=c++11 -O2 src2/testB/testB.cpp
g++ -o src2/testB/testB src2/testB/testB.o -lpthread
scons: done building targets.
```

## References
[1] SCons User Guide, Chapter 14. Hierarchical Builds ([http://scons.org/doc/production/HTML/scons-user/ch14.html#idm139837655800640](http://scons.org/doc/production/HTML/scons-user/ch14.html#idm139837655800640))