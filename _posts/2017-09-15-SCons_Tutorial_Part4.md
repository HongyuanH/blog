---
layout:            post
title:             "SCons Tutorial Part 4 -- variant_dir"
date:              2017-09-15
tags:              SCons
category:          SCons
author:            hongyuan

---

## VariantDir Project v1.0

Say we have a very simple project:

![VariantDir_v1.0_A.png]({{ site.github.url }}/res/2017-09-SCons_Tutorial/VariantDir_v1.0_A.png#left)
<div style="clear:left"></div>

<div class="div-nm">Sconstruct:</div>
```python{:.line-numbers}
env = Environment()
SConscript("src/SConscript", exports="env")
```

<div class="div-nm">src/SConscript:</div>
```python{:.line-numbers}
Import("env")
env = env.Clone()
env.Program("HelloWorld.cpp")
```

Easy to understand right? But what happens when we run `socns` is that the built targets (i.e. **HelloWorld.o** and **HelloWorld**) are generated in src as well:

![VariantDir_v1.0_B.png]({{ site.github.url }}/res/2017-09-SCons_Tutorial/VariantDir_v1.0_B.png#left)
<div style="clear:left"></div>

Can we move all the generated files into a seperate folder so that if we want to start again we just have to delete that folder? Check our **VariantDir Project v2.0**!


## VariantDir Project v2.0

Modify our `Sconstruct` file:

<div class="div-nm">Sconstruct:</div>
```python{:.line-numbers}
env = Environment()
SConscript("src/SConscript", exports="env", variant_dir="build")
```

In effect, the `variant_dir` argument causes the files (and subdirectories) in the directory where script resides to be copied to `variant_dir` and the build performed in `variant_dir`[1]. In our case, all the files in **src** will be copied into **build** and the actual build will be performed there. This time after running `scons` the project looks like this:

![VariantDir_v2.0_B.png]({{ site.github.url }}/res/2017-09-SCons_Tutorial/VariantDir_v2.0_B.png#left)
<div style="clear:left"></div>

Can we avoid the copy but move only the targets into the **build** folder? Check our **VariantDir Project v3.0**!


## VariantDir Project v3.0

Just add an extract argument `duplicate=False` to `SConscript()`:

<div class="div-nm">Sconstruct:</div>
```python{:.line-numbers}
env = Environment()
SConscript("src/SConscript", exports="env", variant_dir="build", duplicate=False)
```

This time after running `scons`:

![VariantDir_v3.0_B.png]({{ site.github.url }}/res/2017-09-SCons_Tutorial/VariantDir_v3.0_B.png#left)
<div style="clear:left"></div>

At this point, we have talked about almost everything about `SConscript()`. If you are interested, check [this link](https://bitbucket.org/scons/scons/wiki/SConscript()) for more details.

## References
[1] Bitbucket SCons Wiki -- SConscript() ([https://bitbucket.org/scons/scons/wiki/SConscript()](https://bitbucket.org/scons/scons/wiki/SConscript()))