---
layout:            post
title:             "SCons Tutorial Part 6 -- Glob & filter"
date:              2017-09-17
tags:              SCons
category:          SCons
author:            hongyuan

---

## Glob & filter

In SCons build script we can use the method `Glob()` to automaticlly search for files in a specific directory so that we don't need to type in every single file name ourself. If some files need to be filtered out from the result returning by `Glob()`, `filter()` can be used.

## Example

<div class="div-nm">Project Layout:</div>
```
GlobAndFilter
|--dirA
|  |--testA1.cpp
|  |--testA2.cpp
|  |--testA3.cpp
|--dirB
|  |--dir1
|  |  |--filterMe.cpp
|  |  |--testB11.cpp
|  |  |--testB12.cpp
|  |--dir2
|     |--testB21.cpp
|     |--testB22.cpp
|--Sconstruct
```

<div class="div-nm">Sconstruct:</div>
```python{:.line-numbers}
import os

def cppFilter(cppFile):
    return os.path.basename(cppFile.path) != "filterMe.cpp"

env = Environment()
for root, dirs, files in os.walk(".", topdown=False):
    for dir in dirs:
        allCpp = Glob("%s/*.cpp" % os.path.join(root, dir))
        allCppFiltered = filter(cppFilter, allCpp) 
        for cppFile in  allCppFiltered:
            print "*** Adding %s to targets..." % cppFile
            env.Object(cppFile)
```

The task here is to recursively search for **.cpp** files under the root directory (".") and compile them into **.o** files expect the file **filterMe.cpp**. There're three stages of for loops in the script:

1. Use the Python `os.walk()` to recursively walk through all the directories.
2. For each directory, use `Glob()` to search for all the **.cpp** files using a wild card `"%s/*.cpp" % os.path.join(root, dir)`. The path needs to be given because Glob can only search for files under a specfic directory.
3. For each file returned by `Glob()` except **filterMe.cpp**, compile them into an object file using `env.Object()`.

The usage of `Glob()` should be quite straghtforward. But not `filter()`. `filter()` takes two arguments:

* the first one is a function that accepts an object of class `SCons.Node.FS.File` (check [this link](http://scons.org/doc/HTML/scons-api/SCons.Node.FS.File-class.html) for more details about this class) as input and returns a boolean indicating whether a file should be filtered out;
* the second one is the `Glob` object to be filtered.

Most commonly, inside the filter function (`cppFilter()` in this case), a rule can be applied by checking the `path` property (a string indicating the path of a file relative to the root directory) of the input object (`cppFile`). In our example, if `cppFile.path` is `"filterMe.cpp"`, `False` is returned, `True` otherwise.

Try to compile the project:

```bash
$ cd GlobAndFilter
$ ld
dirA  dirB  Sconstruct
$ scons
scons: Reading SConscript files ...
*** Adding dirB/dir2/testB21.cpp to targets...
*** Adding dirB/dir2/testB22.cpp to targets...
*** Adding dirB/dir1/testB11.cpp to targets...
*** Adding dirB/dir1/testB12.cpp to targets...
*** Adding dirA/testA1.cpp to targets...
*** Adding dirA/testA2.cpp to targets...
*** Adding dirA/testA3.cpp to targets...
scons: done reading SConscript files.
scons: Building targets ...
g++ -o dirA/testA1.o -c dirA/testA1.cpp
g++ -o dirA/testA2.o -c dirA/testA2.cpp
g++ -o dirA/testA3.o -c dirA/testA3.cpp
g++ -o dirB/dir1/testB11.o -c dirB/dir1/testB11.cpp
g++ -o dirB/dir1/testB12.o -c dirB/dir1/testB12.cpp
g++ -o dirB/dir2/testB21.o -c dirB/dir2/testB21.cpp
g++ -o dirB/dir2/testB22.o -c dirB/dir2/testB22.cpp
scons: done building targets.
```

Clean:

```bash
$ scons -c
scons: Reading SConscript files ...
*** Adding dirB/dir2/testB21.cpp to targets...
*** Adding dirB/dir2/testB22.cpp to targets...
*** Adding dirB/dir1/testB11.cpp to targets...
*** Adding dirB/dir1/testB12.cpp to targets...
*** Adding dirA/testA1.cpp to targets...
*** Adding dirA/testA2.cpp to targets...
*** Adding dirA/testA3.cpp to targets...
scons: done reading SConscript files.
scons: Cleaning targets ...
Removed dirA/testA1.o
Removed dirA/testA2.o
Removed dirA/testA3.o
Removed dirB/dir1/testB11.o
Removed dirB/dir1/testB12.o
Removed dirB/dir2/testB21.o
Removed dirB/dir2/testB22.o
scons: done cleaning targets.
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