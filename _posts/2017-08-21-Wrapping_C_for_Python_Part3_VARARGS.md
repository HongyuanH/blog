---
layout:            post
title:             "Wrapping C/C++ for Python: Part 3--VARARGS IO"
date:              2017-08-21
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

<div class="div-nm">varargs_io_wrapper.cpp:</div>
```cpp{:.line-numbers}
#include <Python.h>
#include <iostream>

using namespace std;

static PyObject*
varargs_io_wrapper(PyObject * self, PyObject * args){
	int int_input;
	float float_input;
	if (!PyArg_ParseTuple(args, "if", &int_input, &float_input)){
		return NULL;
	}

	cout << "[   C++]Got int_input: " << int_input << endl;
	cout << "[   C++]Got float_input: " << float_input << endl;

	int int_output = int_input + 1;
	float float_output = float_input * 2;
	return Py_BuildValue("if", int_output, float_output);
}

PyMODINIT_FUNC
initvarargs_io_module(void)
{
	static PyMethodDef method_list[] = {
		{ "varargs_io", (PyCFunction)varargs_io_wrapper,  METH_VARARGS, NULL},
		{ NULL, NULL, 0, NULL } // end of methods
	};
	Py_InitModule3("varargs_io_module", method_list, NULL);
}
```

<div class="div-nm">setup.py:</div>
```python{:.line-numbers}
from distutils.core import setup, Extension
extension_mod = Extension("varargs_io_module", 
                          sources=["varargs_io_wrapper.cpp"])
setup(ext_modules=[extension_mod])
```

**2. Usage**{:.em-uln}

Same trick again:

```bash
python setup.py build_ext --inplace
```

<div class="div-nm">main.py:</div>
```python{:.line-numbers}
import varargs_io_module

int_input = 1
float_input = 2.0

int_output, float_output = varargs_io_module.varargs_io(int_input, float_input)

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

**3. About varargs_io_wrapper.cpp**{:.em-uln}

The input arguments to a **PyCFunction** wrapper function is passed in as a tuple, as the second input(i.e. the **args** pointer). Function **PyArg_ParseTuple()** should be used to copy elements into different variables one by one, for example:

```cpp
PyArg_ParseTuple(args, "if", &int_input, &float_input)
```

takes the tuple pointer as the first argument and the format string as the second, followed by the variadic arguments (very similar to `printf()`). The first character `'i'` in the format string tells the function the first element of the input tuple is an integer, and `'f'` means the second element of the tuple is a float. These two values are then copied into the the given address `&int_input` and `&float_input`.

Then we can do whatever we like with the inputs:

```cpp
	cout << "[   C++]Got int_input: " << int_input << endl;
	cout << "[   C++]Got float_input: " << float_input << endl;
```

Next, build and return a new tuple back to Python using another function, **Py_BuildValue()**, using a format string as well (<span style="color:red;">write down the variables themselves instead of their addresses as the input</span>):

```cpp
	int int_output = int_input + 1;
	float float_output = float_input * 2;
	return Py_BuildValue("if", int_output, float_output);
```


Finally, we need to tell Python interpreter that our wrapper function, **varargs_io_wrapper()**, accepts **METH_VARARGS** instead of **METH_NOARGS**, meaning that the wrapper function takes some input variable arguments instead of no input arguments (hence the title of this post). This is done in line 26 of varargs_io_wrapper.cpp:

```cpp
{ "varargs_io", (PyCFunction)varargs_io_wrapper,  METH_VARARGS, NULL},
```

**4. Optional argument**{:.em-uln}

Haven't got time to write this part yet :)

## Links
Parsing arguments and building values ([https://docs.python.org/2/c-api/arg.html](https://docs.python.org/2/c-api/arg.html))