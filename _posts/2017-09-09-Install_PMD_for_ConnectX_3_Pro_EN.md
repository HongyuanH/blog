---
layout:            post
title:             "Install PMD for ConnectX-3 Pro EN"
date:              2017-09-09
tags:              Mellanox PMD DPDK
category:          Others
author:            hongyuan

---

## Prerequisites
 * Mellanox ConnectX-3 Pro EN
 * Ubuntu 14.04(x86_64)

## Install MLNX_OFED

The **ConnectX-3 Pro EN** NIC card comes with firmware version `2.36.5150`, by checking [this link](http://www.mellanox.com/page/mlnx_ofed_matrix?mtag=linux_sw_drivers), the matching driver shoule be `MLNX_OFED 3.4-1.0.0.0`. To download the corresponding driver for Ubuntu 14.04(x86_64), click [here](http://www.mellanox.com/page/mlnx_ofed_eula?mtag=linux_sw_drivers&mrequest=downloads&mtype=ofed&mver=MLNX_OFED-3.4-1.0.0.0&mname=MLNX_OFED_LINUX-3.4-1.0.0.0-ubuntu14.04-x86_64.tgz).

Alternatively, go to [this page](http://www.mellanox.com/page/products_dyn?product_family=26&mtag=linux) and find the correct driver from Archive Versions:

![download_MLNX_OFED]({{ site.github.url }}/res/2017-09-09-Install_PMD_for_ConnectX_3_Pro_EN/download_MLNX_OFED.png#middle)

To install the driver, generally a `sudo ./mlnxofedinstall` will work. However, to support the low-latency kernel **dkms (Dynamic Kernel Module Support)** needs to be disabled and the low-latency kernel number should be specified. Below is the command:

```bash
tar -zxvf MLNX_OFED_LINUX-3.4-1.0.0.0-ubuntu14.04-x86_64.tgz
cd MLNX_OFED_LINUX-3.4-1.0.0.0-ubuntu14.04-x86_64
sudo ./mlnxofedinstall --without-dkms --add-kernel-support --kernel 3.17.0-031700-lowlatency --without-fw-update --force
```

Configure the driver and restart:

```bash
echo 'options mlx4_core log_num_mgm_entry_size=-7' | sudo tee /etc/modprobe.d/mlx4_core.conf
sudo /etc/init.d/openibd restart
```

## Install MLNX_DPDK

MLNX_DPDK is eseentially the **PMD (Poll Mode Driver)** for Mellanox NIC cards. The latest version supporting `MLNX_OFED 3.4-1.0.0.0` is `MLNX_DPDK 16.11_2.3`.Click this [link](http://www.mellanox.com/downloads/Drivers/MLNX_DPDK_16.11_2.3.tar.gz) to download.

Extract and install PMD to **/opt/mlnx_dpdk**:

```bash
tar -zxvf MLNX_DPDK_16.11_2.3.tar.gz
cd MLNX_DPDK_16.11_2.3
sudo make install T=x86_64-native-linuxapp-gcc DESTDIR=/opt/mlnx_dpdk
```

## References
[1] Mellanox OFED for Linux User Manual Rev 3.40 ([http://www.mellanox.com/related-docs/prod_software/Mellanox_OFED_Linux_User_Manual_v3.40.pdf](http://www.mellanox.com/related-docs/prod_software/Mellanox_OFED_Linux_User_Manual_v3.40.pdf))  
[2] Mellanox DPDK Quick Start Guide Rev 16.11_2.3 ([http://www.mellanox.com/related-docs/prod_software/MLNX_DPDK_Quick_Start_Guide_v16.11_2.3.pdf](http://www.mellanox.com/related-docs/prod_software/MLNX_DPDK_Quick_Start_Guide_v16.11_2.3.pdf))  
