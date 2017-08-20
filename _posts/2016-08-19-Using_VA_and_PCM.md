---
layout:            post
title:             "Intel VTune Amplifier & Performance Counter Monitor"
date:              2016-08-19
tags:              Profiling Performance
category:          Programming for Performance
author:            hongyuan

---

This post shows you how to use Intel VTune Amplifier and Intel Performance Counter Monitor for performance profiling in Linux (I'm using Ubuntu 14.04).

## VTune Amplifier
**1. Download**{:.em-uln}

  * Open [this page](https://software.intel.com/en-us/intel-vtune-amplifier-xe), choose the **Linux*** version and then click **Download FREE Trial**. You will need to fill in a form for Intel in order to download the package.

**2. Installation**{:.em-uln}

  * There is an offical guide for installation which can be found [here](https://software.intel.com/sites/default/files/managed/26/a8/amplxe_install_guide_linux_0.pdf).
  * Alternatively, after the download has completed, just launch the installer and follow the instructions:

```bash
tar -xzf vtune_amplifier_xe_*.tar.gz
cd vtune_amplifier_xe_*
./install_GUI.sh
```

  * The default installation directory of VTune Amplifier is **/opt/intel/**, in this case, you may want to establish the environment for VTune Amplifier by doing `source /opt/intel/vtune_amplifier_xe/amplxe-vars.sh`.

**3. Usage**{:.em-uln}

 * Read the [documentation](https://software.intel.com/en-us/intel-vtune-amplifier-xe-support/documentation).
 * Read the [Tutorials](https://software.intel.com/en-us/articles/intel-vtune-amplifier-tutorials).


## Performance Counter Monitor (PCM)

**1. Download**{:.em-uln}

 * Download the package from [here](https://software.intel.com/en-us/articles/intel-performance-counter-monitor).

**2. Installation**{:.em-uln}

```bash
unzip IntelPerformanceCounterMonitor-*.zip
cd IntelPerformanceCounterMonitor-*
make
```

List the utilities that have been compiled:

```bash
ls *.x
```

**3. Usage**{:.em-uln}

 * Before using PCM you may want to read this [introduction page](https://software.intel.com/en-us/articles/intel-performance-counter-monitor) first.
 * To see the counters:

```bash
echo 0 | sudo tee /proc/sys/kernel/nmi_watchdog     # disable watchdog
sudo modprobe msr                                   # enable msr module
sudo ./pcm.x
```

 * or the memory metrics:

```bash
sudo ./pcm-memory.x
```

 * To use the GUI, install **KSysGuard** first and then follow the instructions in the file **KSysGuard HOWTO.pdf**, which can be found in the root directory of PCM.
 * To monitor the counters inside your program, take a look at the examples(pcm.cpp, pcm-memory.cpp, etc.) in the root direcotry of PCM.

