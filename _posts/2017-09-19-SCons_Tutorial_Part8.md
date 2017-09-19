---
layout:            post
title:             "SCons Tutorial Part 8 -- LINKCOM"
date:              2017-09-19
tags:              SCons
category:          SCons
author:            hongyuan

---

## LINKCOM

Sometimes we need to use the option `-Wl,--start-group -Wl,--end-group` to resolve circular dependencies at linking. For example, to statically link with the [Intel MKL](https://software.intel.com/en-us/mkl) library, we need to use a link line like this:

```bash
-Wl,--start-group $MKL_ROOT/lib/intel64/libmkl_intel_lp64.a $MKL_ROOT/lib/intel64/libmkl_sequential.a $MKL_ROOT/lib/intel64/libmkl_core.a -Wl,--end-group -lpthread -lm -ldl
```

Unfortunately, SCons does not provide any support for the `-Wl,--start-group -Wl,--end-group` option. However, the problem can be resolved by overwriting the default `LINKCOM` construction variable, which stands for **link command**.


## Example

<div class="div-nm">Project Layout:</div>
```
LINKCOM
|--main.cpp
|--Sconstruct
```

<div class="div-nm">main.cpp:</div>
```cpp{:.line-numbers}}
#include <mkl.h>
#include <mkl_vsl.h>

int main(){
	DFTI_DESCRIPTOR_HANDLE fft_handle;
	MKL_LONG status;
	MKL_Complex8 in[512];
	MKL_Complex8 out[512];
	status = DftiCreateDescriptor(&fft_handle, DFTI_SINGLE, DFTI_COMPLEX, 1, 512);
	status = DftiCommitDescriptor(fft_handle);
	status = DftiSetValue(fft_handle, DFTI_PLACEMENT, DFTI_NOT_INPLACE);
	status = DftiCommitDescriptor(fft_handle);
	DftiComputeBackward(fft_handle, in, out);
	return 0;
}
```

<div class="div-nm">Sconstruct:</div>
```python{:.line-numbers}
env = Environment()
# CPPPATH
env["CPPPATH"] = []
env["CPPPATH"] += ["/opt/intel/mkl/include"]
# LIBS
env["LIBS"] = []
env["LIBS"] += ["pthread"]
env["LIBS"] += ["m"]
env["LIBS"] += ["dl"]
# MKL static LIBS
env["MKLLIBS"] = []
env["MKLLIBS"] += ["/opt/intel/mkl/lib/intel64/libmkl_intel_lp64.a"]
env["MKLLIBS"] += ["/opt/intel/mkl/lib/intel64/libmkl_sequential.a"]
env["MKLLIBS"] += ["/opt/intel/mkl/lib/intel64/libmkl_core.a"]
# overwrite SCons LINKCOM
env["LINKCOM"] = "$LINK -o $TARGET $LINKFLAGS $__RPATH $SOURCES $_LIBDIRFLAGS -Wl,--start-group $MKLLIBS -Wl,--end-group $_LIBFLAGS"
env.Program("main.cpp")
```

The idea is to modify SCons's default link command so that at link stage SCons will use our customized command to do whatever we want at that stage. By default, SCons's `LINKCOM` is:

```bash
$LINK -o $TARGET $LINKFLAGS $__RPATH $SOURCES $_LIBDIRFLAGS
```

Each symbol (those starting with an `$`) inside the command will eventually be replaced with the corresponding variable in the construction envrionment. For example, `$LINK` will be replaced by `env["LINK"]` and `$TARGET` will become `env["TARGET"]`. You may want to find out what each symbol means at [this page](http://www.scons.org/doc/HTML/scons-user/apa.html).

What we need to do here is to add the option `-Wl,--start-group -Wl,--end-group` between `$_LIBDIRFlAGS` and `$_LIBFLAGS`. To allow using different libraries of MKL, a new construction variable `MKLLIBS` is created and `env["LINKCOM"]` finally becomes:

```bash
$LINK -o $TARGET $LINKFLAGS $__RPATH $SOURCES $_LIBDIRFLAGS -Wl,--start-group $MKLLIBS -Wl,--end-group $_LIBFLAGS
```

Try to compile the project:

```bash
$ cd LINKCOM
$ ls
main.cpp  Sconstruct
$ scons
scons: Reading SConscript files ...
scons: done reading SConscript files.
scons: Building targets ...
g++ -o main.o -c -I/opt/intel/mkl/include main.cpp
g++ -o main main.o -Wl,--start-group /opt/intel/mkl/lib/intel64/libmkl_intel_lp64.a /opt/intel/mkl/lib/intel64/libmkl_sequential.a /opt/intel/mkl/lib/intel64/libmkl_core.a -Wl,--end-group -lpthread -lm -ldl
scons: done building targets.
```