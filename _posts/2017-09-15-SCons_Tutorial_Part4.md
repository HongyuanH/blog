---
layout:            post
title:             "SCons Tutorial Part 4 -- variant_dir"
date:              2017-09-15
tags:              SCons
category:          SCons
author:            hongyuan

---

## Example -- VariantDir\_v1.0

<div class="div-nm">Project Layout:</div>
```
VariantDir_v1.0
|--src
|  |--HelloWorld.cpp
|  |--SConscript
|--Sconstruct
```

<div class="div-nm">Sconstruct:</div>
```python
env = Environment()
SConscript("src/SConscript", exports="env")
```

<div class="div-nm">src/SConscript:</div>
```python
Import("env")
env = env.Clone()
env.Program("HelloWorld.cpp")
```

Easy to understand right? However when we issue `scons` the build targets (i.e. **HelloWorld.o** and **HelloWorld**) are generated in **src** as well:

```
VariantDir_v1.0
|--src
|  |--HelloWorld.cpp
|  |--SConscript
|  |--HelloWorld.o
|  |--HelloWorld
|--Sconstruct
```


Can we move all the generated files into a seperate folder so that if we want to start again we just have to delete that folder? Check our **VariantDir\_v2.0**!


## Example -- VariantDir\_v2.0

Modify our `Sconstruct` file:

<div class="div-nm">Sconstruct:</div>
```python
env = Environment()
SConscript("src/SConscript", exports="env", variant_dir="build")
```

In effect, the `variant_dir` argument causes the files (and subdirectories) in the directory where script resides to be copied to `variant_dir` and the build performed in `variant_dir`[1]. In our case, all the files in the folder **src** will be copied into another folder **build** and the actual build will be performed there. This time after running `scons` the project looks like this:

```
VariantDir_v2.0
|--build
|  |--HelloWorld.cpp
|  |--SConscript
|  |--HelloWorld.o
|  |--HelloWorld
|--src
|  |--HelloWorld.cpp
|  |--SConscript
|--Sconstruct
```

Can we avoid the copy but move only the targets into the **build** folder? Check our **VariantDir\_v3.0**!


## Example -- VariantDir\_v3.0

Add an extract argument `duplicate=False` to `SConscript()`:

<div class="div-nm">Sconstruct:</div>
```python
env = Environment()
SConscript("src/SConscript", exports="env", variant_dir="build", duplicate=False)
```

This time after running `scons`, exactly what we need:

```
VariantDir_v3.0
|--build
|  |--HelloWorld.o
|  |--HelloWorld
|--src
|  |--HelloWorld.cpp
|  |--SConscript
|--Sconstruct
```

At this point, we have talked about almost everything about `SConscript()`. If you are interested, check [this link](https://bitbucket.org/scons/scons/wiki/SConscript()) for more details.

## References
[1] Bitbucket SCons Wiki -- SConscript() ([https://bitbucket.org/scons/scons/wiki/SConscript()](https://bitbucket.org/scons/scons/wiki/SConscript()))