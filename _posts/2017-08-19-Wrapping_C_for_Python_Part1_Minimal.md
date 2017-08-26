---
layout:            post
title:             "Wrapping C/C++ for Python: Part 1--The Minimal Example"
date:              2017-08-19
tags:              Python C C++
category:          Python
author:            hongyuan
---


## Prerequisite
 * Python 2.7
 * python-dev (debian package, can be installed by apt-get in Ubuntu)
 * GCC/G++



## The Minimal Example
**1. Sources**{:.em-uln}

<div class="div-nm">minimal_wrapper.cpp:</div>

```cpp{:.line-numbers}
#include <Python.h>
PyMODINIT_FUNC initminimal_module(void)
{
	Py_InitModule3("minimal_module", NULL, NULL);
}
```

<div class="div-nm">setup.py:</div>

```python{:.line-numbers}
from distutils.core import setup, Extension
extension_mod = Extension(name="minimal_module",
                          sources=["minimal_wrapper.cpp"])
setup(ext_modules=[extension_mod])
```

**2. Usage**{:.em-uln}

There're two files here: 
 * the C\+\+ wrapper (which defines the module, and wraps all the existing C/C\+\+ functions)
 * the setup script (to create a Python loadable module).

With these two files sitting in the same directory, use the command:

```bash
python setup.py build_ext --inplace
```

to build a shared object **minimal_module.so** (`--inplace` tells Python to generate the .so file in the current directory). Now you already have a working C extension module for python:

<div class="div-nm">main.py:</div>

```python{:.line-numbers}
import minimal_module
print minimal_module.__dict__
```

**3. Some Explanations**{:.em-uln}

The procedure for Python to `import minial_module` is:

 1. The interpreter searches for **minial_module** in its module search path, according to the [Official Doc](https://docs.python.org/2/tutorial/modules.html#the-module-search-path), this consists of:
   * the directory containing the input script (or the current directory).
   * PYTHONPATH (a list of directory names, with the same syntax as the shell variable PATH).
   * the installation-dependent default.

 2. When the file **mininal_module.so** is found, the interpreter looks for the module init function **initminial_module** (see line 3 in minimal\_wrapper.cpp) and runs it. A module init function is decorated by the preprocessor **PyMODINIT_FUNC** and its function name must be init**_MODULE\_NAME_**, i.e. init<span style="color:red">minimal_module</span> in our case.

 3. Inside the module init function, **Py_InitModule3()** is called (line 5), this will register the module in the local namespace so that you can access the module via **_MODULE\_NAME_** afterwards.

**_MODULE\_NAME_** ('minimal_module'), occurs 3 times in our example:

 * In the name of the function decorated by **PyMODINIT_FUNC** (line 3 of minimal_wrapper.cpp)
 * In the call of **Py_InitModule3()** (line 5 of minimal_wrapper.cpp)
 * In the call of **Extension()** (line 2 of setup.py)

If you are wrting your own C extension, <span style="color:red">make sure the module name in these three places are consistent (case sensitive)</span>, e.g. for an extension module called '**example_module**', you would write something like:

<div class="div-nm">example_wrapper.cpp:</div>

```cpp{:.line-numbers}
#include <Python.h>

PyMODINIT_FUNC initexample_module(void)
{
	Py_InitModule3("example_module", NULL, NULL);
}
```

<div class="div-nm">setup.py:</div>

```python{:.line-numbers}
from distutils.core import setup, Extension
extension_mod = Extension(name="example_module",
                          sources=["wrapper.cpp"])
setup(ext_modules=[extension_mod])
```

`python setup.py build_ext --inplace` will generate **example_module.so** for you.

If you see:

> ImportError: dynamic module does not define init function (initminimal_module)

or:

> SystemError: dynamic module not initialized properly

check the module name consistency in these three places.

**4. About setup.py**{:.em-uln}

The setup script acts like a configuration file for invoking compiler. In the example here, we have only used two arguments when instantiating an object of **Extension**: name and sources. <span style="color:red">Most of the arguments are in the form of list so even if you only have element you need to use brakets to include them like `sources=["wrapper.cpp"]` instead of `sources="wrapper.cpp"`</span>.

Below is a template for building extension which might be helpful:

```python{:.line-numbers}
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

You can find the full API reference of the class **Extension** [here](https://docs.python.org/2/distutils/apiref.html#distutils.core.Extension).


## Useful Links
* Official doc: [The import statement](https://docs.python.org/2.7/reference/simple_stmts.html#import)
* Official doc: [Python module](https://docs.python.org/2/tutorial/modules.html#modules)
* [Python Modules](http://www.tutorialspoint.com/python/python_modules.htm) on Tutorialspoint
* Official doc: [Writing the Setup Script](https://docs.python.org/2/distutils/setupscript.html)

## References

\[1] Your First Python extension ([http://starship.python.net/crew/mwh/toext/your-first-extension.html](http://starship.python.net/crew/mwh/toext/your-first-extension.html))