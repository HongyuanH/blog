---
layout:            post
title:             "Wrapping C/C++ for Python: Part 2--Hello World"
date:              2017-08-20
tags:              Python C C++
category:          Python
author:            hongyuan

---



## Prerequisite
 * Python 2.7
 * python-dev (debian package, can be installed by apt-get in Ubuntu)
 * GCC/G++


## Hello World

**1. Sources**{:.em-uln}

This time we have two more C\+\+ files: 'hello\_world.hpp' and 'hello\_world.cpp'. They contain the C\+\+ function that you want to wrap for Python:
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

**2. Usage**{:.em-uln}

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

We need to do 3 things in this file:

* Wrap existing functions  
  Everything in Python is an object, so we define a function that takes and returns pointer of **PyObject**. Because the wrapper function itself is also an object, so we need to have an extra 'self' pointer as the first argument. Inside the wrapper function, **hello_world()** is called and then the macro **Py_RETURN_NONE** is used to return a None pointer to Python.

* Write a Method Mapping Table  
  A Method Mapping Table can be created with an array of **PyMethodDef**. According to the [official doc](https://docs.python.org/2/c-api/structures.html#c.PyMethodDef), **PyMethodDef** consists of four fields:
   * **ml_name**: the name of the method that can be used in Python.
   * **ml_meth**: the entry of a C function that will be executed when the method is called in Python. In our case, when **hello_world()** is called in Python, **hello_world_wrapper()** gets executed.
   * **ml_flags**: specify what kind of inputs are acceptable for this method, available options are **METH_NOARGS**, **METH_VARARGS**, **METH_KEYWORDS**, and **METH_VARARGS\|METH_KEYWORDS**. In our example, **METH_NOARGS** tells Python that the method **hello_world()** takes no arguments. <span style="color:red">The Method Mapping Table must end with {NULL, NULL, 0, NULL}</span>.
   * **ml_doc**: the docstring for the function, which could be NULL if you do not feel like writing one

* Write a module init function  
  Basically the same as the previous example except that the Method Mapping Table is passed into **Py_InitModule3()**.


**4. About setup.py**{:.em-uln}

This time we use another parameter to initialise **Extention**: `include_dirs=["."]`, this simply tells Python to add the current directory to compiler include path so that `#include <hello_world.hpp>` is legal.


## References
\[1] Python Extension Programming with C ([http://www.tutorialspoint.com/python/python_further_extensions.htm](http://www.tutorialspoint.com/python/python_further_extensions.htm))