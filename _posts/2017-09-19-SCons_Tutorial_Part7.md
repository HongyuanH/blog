---
layout:            post
title:             "SCons Tutorial Part 7 -- Adding Command Line Options"
date:              2017-09-19
tags:              SCons
category:          SCons
author:            hongyuan

---

## Adding Command Line Options[1]

A common requirement of build system is to allow users to add custome command line option to control the build (e.g. debug/release build type). In SCons, there're two different ways of accomplishing this:

* adding command line build variables by creating a `Variables` object and using the `Add()` method
* adding command line option via the `AddOption()` method



## Example

<div class="div-nm">Project Layout:</div>
```
CommandLine
|--src
|  |--HelloWorld.cpp
|--Sconstruct
```

<div class="div-nm">Sconstruct:</div>
```python{:.line-numbers}
vars = Variables(None, ARGUMENTS)
vars.Add(EnumVariable('BUILD_TYPE', 'type of build to use', 'all',  allowed_values=('debug', 'release', 'all')))
env = Environment(variables=vars)

def add_target(build_dir, ccflags):
    print "*** Adding targets to '%s'..." % build_dir
    envc = env.Clone()
    envc["CCFLAGS"] = ccflags
    envc.Object(target="%s/HelloWorld.o"%build_dir, source="src/HelloWorld.cpp")
    envc.Program(target="%s/HelloWorld"%build_dir, source="%s/HelloWorld.o"%build_dir)

if env["BUILD_TYPE"] == "debug":
    add_target("debug", ["-g"])
elif env["BUILD_TYPE"] == "release":
    add_target("delease", ["-O3"])
elif env["BUILD_TYPE"] == "all":
    add_target("debug", ["-g"])
    add_target("release", ["-O3"])
```

In this example, we use the first approach (add build variables) to allows us to control the build type with a command line variable `BUILD_TYPE`:

* to build for debug type, issue `scons BUILD_TYPE=debug`
* to build for release, issue `scons BUILD_TYPE=release`
* to build for both types, issue `scons BUILD_TYPE=all`
* default to build for both types

SCons provides the `ARGUMENTS` dictionary and the `ARGLIST` list containing the command line arguments (as strings). Different types of variables can be added to the `var` object[1]:

* `UnknownVariables()` to retrieve command line arguments unknown to the Variables class
* `BoolVariable()` to handle arguments with only true/false value
* `ListVariable()` to handle arguments that can hold several values at once (BUILD\_TYPE=debug,release,all,...)
* `PathVariable()` to handle arguments whose value is a path (CONFIG\_FILE=/path/to/my/config)
* `PackageVariable()` to handle arguments whose value is a package that can be enabled/disabled

Another apporach is to use the method `AddOption()`. However it is not within the scope of this post. Check [this link](https://bitbucket.org/scons/scons/wiki/UsingCommandLineArguments) for more details about it.

## References
[1] Bitbucket SCons Wiki -- UsingCommandLineArguments ([https://bitbucket.org/scons/scons/wiki/UsingCommandLineArguments](https://bitbucket.org/scons/scons/wiki/UsingCommandLineArguments))