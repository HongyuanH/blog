---
layout:            post
title:             "Wrapping C/C++ for Python: Part 4--KEYWORDS IO"
date:              2017-08-26
tags:              Python C C++
category:          Python
author:            hongyuan

---



## Prerequisite
 * Python 2.7
 * python-dev (debian package, can be installed by apt-get in Ubuntu)
 * GCC/G++



## The VARARGS IO Example

**1. Sources**{:.em-uln}

<div class="div-nm">keywords_io_wrapper.cpp:</div>
```cpp{:.line-numbers}
#include <Python.h>
#include <iostream>

using namespace std;

static PyObject*
keywords_io_wrapper(PyObject * self, PyObject * args, PyObject *kargs){
	static char *klist[] = {"int_input", "float_input", NULL};
	int int_input;
	float float_input;
	if (!PyArg_ParseTupleAndKeywords(args, kargs, "if", klist, &int_input, &float_input)){
		return NULL;
	}

	cout << "[   C++]Got int_input: " << int_input << endl;
	cout << "[   C++]Got float_input: " << float_input << endl;

	int int_output = int_input + 1;
	float float_output = float_input * 2;
	return Py_BuildValue("if", int_output, float_output);
}


PyMODINIT_FUNC
initkeywords_io_module(void)
{
	static PyMethodDef method_list[] = {
		{ "keywords_io", (PyCFunction)keywords_io_wrapper,  METH_VARARGS|METH_KEYWORDS, NULL},
		{ NULL, NULL, 0, NULL } // end of methods
	};
	Py_InitModule3("keywords_io_module", method_list, NULL);
}

```

<div class="div-nm">setup.py:</div>
```python{:.line-numbers}
from distutils.core import setup, Extension
extension_mod = Extension("keywords_io_module", 
                          sources=["keywords_io_wrapper.cpp"])
setup(ext_modules=[extension_mod])
```

**2. Usage**{:.em-uln}

Same trick again:

```bash
python setup.py build_ext --inplace
```

<div class="div-nm">main.py:</div>
```python{:.line-numbers}
import keywords_io_module
int_output, float_output = keywords_io_module.keywords_io(float_input=2.0, int_input=1)
print "[Python]Got int_output:", int_output
print "[Python]Got float_output:", float_output
```

You should see the output:

```bash
[   C++]Got int_input: 1
[   C++]Got float_input: 2
[Python]Got int_output: 2
[Python]Got float_output: 4.0
```

**3. About keywords_io_wrapper.cpp**{:.em-uln}

This example is very similar to the previous one ([Wrapping C/C++ for Python: Part 3--VARARGS IO]({{ site.github.url }}/blog/python/Wrapping_C_for_Python_Part3_VARARGS)).
The differences are illustrated below:

First, the third argument `PyObject *kargs` is added to the wrapper function so that the keyword dict can be accessed inside. In our example this keyword dict is `{"int_input": int_input, "float_intput": float_intput}`.

Second, **PyArg_ParseTuple()** is replaced with:

```cpp
PyArg_ParseTupleAndKeywords(args, kargs, "if", klist, &int_input, &float_input)
```

which takes two more argumnets:**kargs** and **klist** where **kargs** is the keyword dict, and **klist** is an array of the keys and must be defined in C:

```cpp
static char *klist[] = {"int_input", "float_input", NULL};
```

Finally, we need to tell Python interpreter that our wrapper function, **keywords_io_wrapper()**, accepts both **METH_VARARGS** and **METH_KEYWORDS** using the **OR** operator:

```cpp
{ "keywords_io", (PyCFunction)keywords_io_wrapper,  METH_VARARGS|METH_KEYWORDS, NULL}
```

## Links
Parsing arguments and building values ([https://docs.python.org/2/c-api/arg.html](https://docs.python.org/2/c-api/arg.html))