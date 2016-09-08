---
layout:            post
title:             "Wrapping C/C++ for Python: Part 2"
date:              2016-09-09
tags:              Python C/C++
category:          Others
author:            hongyuan

---



below is a template which might be helpful:

```python
# A template for generating C extension
from distutils.core import setup, Extension
extension_mod = Extension(name="",                          #module name, i.e. the file name of the generated .so file
                          sources=[""],                     #a list of source files
                          include_dirs=None,                #equivalent to -I in GCC/G++
                          define_macros=None,               #equivalent to -D in GCC/G++
                          undef_macros=None,                #equivalent to -U in GCC/G++
                          library_dirs=None,                #equivalent to -L in GCC/G++
                          libraries=None,                   #equivalent to -l in GCC/G++
                          extra_objects=None,               #for static library, etc.
                          extra_compile_args=None,          #any other compiler arguments
                          language=None,                    #"c", "c++", "objc", or None to automate
                          )
setup(ext_modules=[extension_mod])
```

It is very similar to using a GCC/G++ compiler because in essence Python is using a compiler to do the things for you. So you just copy this file into your project, change the arguments according to your requirement, and then `python setup.py build_ext --inplace`.

You can find the full API reference of **Extension()** in [here](https://docs.python.org/2/distutils/apiref.html#distutils.core.Extension).












## Useful Links
* Learn more about [Writing the Setup Script](https://docs.python.org/2/distutils/setupscript.html)