---
layout:            post
title:             "Wrapping C/C++ for Python: Part 1"
date:              2016-09-08
tags:              Python C C++
category:          Others
author:            hongyuan

---

This tutorial will show you how to wrap existing C/C\++ functionality in Python.



## Prerequisite
 * Python 2.7
 * python-dev (debian package, can be installed by apt-get in Ubuntu)
 * GCC/G++



## The Minimal Example

**1. Source files**{:.em-uln}

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

**2. How to use it**{:.em-uln}

As you can see, there're only two files here: one is the C\+\+ wrapper (it defines the module, and if you have any existing C/C\+\+ functions you will wrap them in this file) and another one is the setup script (for creating a Python loadable module).

Create these two files in your working directory, open a terminal and issue:

```bash
python setup.py build_ext --inplace
```

A shared object **minimal_module.so** will be created in the directory (the `--inplace` option tells Python to generate the .so file in the current directory) and you can use it like:
<div class="div-nm">main.py:</div>
```python{:.line-numbers}
import minimal_module
print minimal_module.__dict__
```



**3. Some explanations**{:.em-uln}

The procedure for Python to `import minial_module` is as follows:

 1. The interpreter searches for the **minial_module** in PYTHONPATH.
 2. When the file **mininal_module.so** is found, the interpreter looks for the module init function **initminial_module** (see line 3 in minimal\_wrapper.cpp) and executes it. A module init function is decorated by the preprocessor **PyMODINIT_FUNC** and <span style="color:red">its function name must be init**_MODULE\_NAME_**, i.e. initminimal_module in our case</span>.
 3. Inside the module init function, **Py_InitModule3()** is called (line 5), which will register the module in the local namespace so that you can access the module afterwards.




One thing worth mentioning here is the **_MODULE\_NAME_** ('minimal_module'), which occurs 3 times in our example:

 * In the name of the function decorated by **PyMODINIT_FUNC**
 * In the call of **Py_InitModule3()**
 * In the call of **Extension()** in setup.py

So if you are wrting your own C extension, <span style="color:red">you need to make sure that the module name in these three places are consistent</span>, e.g. for an extension module called '**example_module**', you would write something like:

<div class="div-nm">wrapper.cpp:</div>
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



## The Hello World Example

**1. Source files**{:.em-uln}

This time we have two more C\+\+ files: 'hello\_world.hpp' and 'hello\_world.cpp'. They contain the C\+\+ function that you want to wrap in Python:
<div class="div-nm">hello_world.hpp:</div>
```cpp{:.line-numbers}
void hello_world();
```

<div class="div-nm">hello_world.cpp:</div>
```cpp{:.line-numbers}
#include <iostream>
#include <hello_world.hpp>

using namespace std;

void hello_world(){
	cout << "hello world!" << endl;
}
```

So here comes our new wrapper and setup script:
<div class="div-nm">hello_world_wrapper.cpp:</div>
```cpp{:.line-numbers}
#include <Python.h>
#include <hello_world.hpp>

static PyObject* hello_world_wrapper(PyObject * self, PyObject * args){
	hello_world();
	Py_RETURN_NONE;
}

PyMODINIT_FUNC inithello_world_module(void) {
	static PyMethodDef method_list[] = {
		{ "hello_world", (PyCFunction)hello_world_wrapper,  METH_NOARGS, NULL},
		{ NULL, NULL, 0, NULL } // end of methods
	};
	Py_InitModule3("hello_world_module", method_list, NULL);
}
```

<div class="div-nm">setup.py:</div>
```python{:.line-numbers}
from distutils.core import setup, Extension
extension_mod = Extension(name="hello_world_module",
                          sources=["hello_world_wrapper.cpp", "hello_world.cpp"],
                          include_dirs=["."]
                          )
setup(ext_modules=[extension_mod])
```

**2. How to use it**{:.em-uln}

Same trick to generate **hello_world.so**:

```bash
python setup.py build_ext --inplace
```

Try to call **hello_world()** in a Python script:
<div class="div-nm">main.py:</div>
```python{:.line-numbers}
import hello_world_module
hello_world_module.hello_world()
```

**3. About hello_world_wrapper.cpp**{:.em-uln}

There're three things we need to do in this file:

* Wrap existing functions  
  Everything in Python is an object, so we define a function that takes and returns pointer of **PyObject**. Because the wrapper function itself is also an object, so we need to have an extra 'self' pointer as the first argument. Inside the wrapper function, **hello_world()** is called and then the macro **Py_RETURN_NONE** is used to return a None pointer to Python.

* Write a Method Mapping Table  
  A Method Mapping Table can be created using **PyMethodDef**. The three important parameters here are:
   * **method name**: the name of the method that can be used in Python.
   * **method rountine**: the entry of a C function that will be executed when the method is called in Python. In our case, when **hello_world()** is called, **hello_world_wrapper()** gets executed.
   * **method argument flags**: specify what kind of inputs are acceptable for this method, available options are **METH_NOARGS**, **METH_VARARGS**, **METH_KEYWORDS**, and **METH_VARARGS\|METH_KEYWORDS**. In our example, it tells Python that the method **hello_world()** takes no arguments.
  <span style="color:red">The Method Mapping Table must end with {NULL, NULL, 0, NULL}</span>.

* Write a module init function  
  Basically the same as the previous example except that the Method Mapping Table is passed into **Py_InitModule3()**.


**4. About setup.py**{:.em-uln}

The setup script acts like a configuration file for invoking compiler. In the two examples presented so far, we have only used three arguments when instantiating an object of **Extension()**: name, sources and include_dirs. What these arguments are for should be self-explanatory. <span style="color:red">Most of the arguments are in the form of list so even if you only have, e.g. one include directory, you need to use **include_dirs=["some_dir"]** instead of **include_dirs="some_dir"**</span>.


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

You can find the full API reference of the class **Extension()** [here](https://docs.python.org/2/distutils/apiref.html#distutils.core.Extension).








## Useful Links
* Official doc: [The import statement](https://docs.python.org/2.7/reference/simple_stmts.html#import)
* Official doc: [Python module](https://docs.python.org/2/tutorial/modules.html#modules)
* [Python Modules](http://www.tutorialspoint.com/python/python_modules.htm) on Tutorialspoint
* Official doc: [Writing the Setup Script](https://docs.python.org/2/distutils/setupscript.html)



## References
\[1] Your First Python extension ([http://starship.python.net/crew/mwh/toext/your-first-extension.html](http://starship.python.net/crew/mwh/toext/your-first-extension.html))  
\[2] Python Extension Programming with C ([http://www.tutorialspoint.com/python/python_further_extensions.htm](http://www.tutorialspoint.com/python/python_further_extensions.htm))