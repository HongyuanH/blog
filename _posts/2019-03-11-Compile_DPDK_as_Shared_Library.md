---
layout:            post
title:             "Memo: Install DPDK 17.11 on Ubuntu 16.04"
date:              2019-03-11
tags:              DPDK
category:          Others
author:            hongyuan

---

## Introduction

On **Ubuntu 18.04**, it is very easy to install DPDK 17.11:

```bash
sudo apt-get install dpdk dpdk-dev
```

However on **Ubuntu 16.04**, `apt-get` would install an ancient version (2.2) of DPDK so we have to compile it from source.

## Prerequisites
 * Ubuntu 16.04(x86_64)

## Download & Compile DPDK

First, download DPDK 17.11 from [here](https://fast.dpdk.org/rel/dpdk-17.11.5.tar.xz) or use the below command:

```bash
wget https://fast.dpdk.org/rel/dpdk-17.11.5.tar.xz
```

If you follow the instructions in [the official guide](https://doc.dpdk.org/guides/linux_gsg/build_dpdk.html) DPDK would be compiled as static library (.a) instead of shared library (.so) so an extra `CONFIG_RTE_BUILD_SHARED_LIB=y` flag is added while compiling:

```bash
tar -xJvf dpdk-17.11.5.tar.xz
cd dpdk-stable-17.11.5/
sudo make install -j$(nprocs) CONFIG_RTE_BUILD_SHARED_LIB=y T=x86_64-native-linuxapp-gcc DESTDIR=/opt/dpdk-stable-17.11.5/x86_64-native-linuxapp-gcc
```

DPDK is installed in `/opt/dpdk-stable-17.11.5/`

## Setup DPDK environment

Next step is to setup the environment for compilers and binaries to find DPDK:

```bash
sudo ln -s /opt/dpdk-stable-17.11.5 /opt/dpdk
sudo sh -c 'echo "
#DPDK library path
/opt/dpdk/x86_64-native-linuxapp-gcc/lib/
" >> /etc/ld.so.conf.d/dpdk.conf'
sudo ldconfig

sudo sh -c 'echo "
#DPDK environment
export RTE_SDK_DIR=/opt/dpdk
export RTE_TARGET=x86_64-native-linuxapp-gcc
export RTE_INCLUDE=\${RTE_SDK_DIR}/\${RTE_TARGET}/include
export LIBRARY_PATH=\$LIBRARY_PATH:\${RTE_SDK_DIR}/\${RTE_TARGET}/lib
" >> /etc/profile'
source /etc/profile
```

It is also very useful to create a shortcut to `dpdk-devbind`

```bash
sudo ln -s /opt/dpdk/x86_64-native-linuxapp-gcc/share/dpdk/usertools/dpdk-devbind.py /usr/local/sbin/dpdk-devbind
```
