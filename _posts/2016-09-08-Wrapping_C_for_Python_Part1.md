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
	Py_InitModule3("minimal_module",
				   NULL,
			       "This is the docstring for minimal_module");
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

As you can see, there're only two files here: one is our C++ wrapper (don't worry about what it means for now) and another one is the setup script. Create these two files in your working directory, then open a terminal and issue:

```bash
python setup.py build_ext --inplace
```

A shared object **minimal_module.so** will be created in the directory. Now you can import it as a Python module by `import minimal_module`. Although there's nothing really meaningful in this module, it is still a complete C extension for Python and you can try to print the \_\_dict\_\_ attribute of it:

```python
import minimal_module
print minimal_module.__dict__
```

The `--inplace` option here tells Python to generate the .so file in the current directory instead of somewhere else (./build/lib.*/).

**3. Some explanations**{:.em-uln}

The procedure for Python to `import minial_module` is as follows:

 1. The interpreter searches for the module **minial_module** in PYTHONPATH.
 2. When the file **mininal_module.so** is found, the interpreter looks for the module init function **initminial_module** (line 3) decorated by the preprocessor **PyMODINIT_FUNC** inside the file and executes it.
 3. Inside the init function, **Py_InitModule3()** is called (line 5), which will register the module in the local namespace so that you can access the module afterwards.  
 The function **Py_InitModule3()** takes 3 arguments:  
  - **module_name**: this is used to register the module, <span style="color: red">it must be exactly the same as your module name</span>, otherwise Python will complain about it.
  - **module_methods**: this is the so called Method Mapping Table which tells Python what methods are defined inside the module. We will come to this shortly.
  - **docstring**: this will become the docstring of the module. You can access it using module\_name.\_\_doc\_\_.

One thing worth mentioning here is the module_name ('minimal_module'), which occurs 3 times in our example:

 * In the name of the function decorated by **PyMODINIT_FUNC**
 * In the call of **Py_InitModule3()**
 * In the call of **Extension()** in setup.py

So if you are wrting your own C extension, <span style="color:red">you need to make sure that the module name in these three places are consistent</span>, e.g. for an extension module called '**example_module**', you would write something like:

<div class="div-nm">wrapper.cpp:</div>
```cpp{:.line-numbers}
#include <Python.h>

PyMODINIT_FUNC initexample_module(void)
{
	Py_InitModule3("example_module",
				   NULL,
			       NULL);
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




**4. The setup script**{:.em-uln}

The setup script (setup.py) is used to generate the shared object when you do `python setup.py build_ext --inplace`. In our minimal example, we only tell Python that the module name is 'minimal_module' and there's one source file 'minimal_wrapper.cpp' to be compiled. Everything else is handled automatically by Python. In a more common situation you would need to pass more arguments to **Extension()**, but for now all you need to know is that we use this file to tell Python (which calls the compiler) how to compile the shared object.



## Useful Links
* Learn more about [the import statement](https://docs.python.org/2.7/reference/simple_stmts.html#import)
* Learn more about [module](https://docs.python.org/2/tutorial/modules.html#modules)
* [Python Modules](http://www.tutorialspoint.com/python/python_modules.htm) on Tutorialspoint


## References
\[1] Your First Python extension ([http://starship.python.net/crew/mwh/toext/your-first-extension.html](http://starship.python.net/crew/mwh/toext/your-first-extension.html))  
\[2] Python Extension Programming with C ([http://www.tutorialspoint.com/python/python_further_extensions.htm](http://www.tutorialspoint.com/python/python_further_extensions.htm))