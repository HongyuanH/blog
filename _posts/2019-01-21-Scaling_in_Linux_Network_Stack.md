---
layout:            post
title:             "Memo: Scaling in Linux Network Stack"
date:              2019-01-21
tags:              Memo Network
category:          Memo
author:            hongyuan

---

## Introduction

[Scaling in the Linux Networking Stack](https://www.kernel.org/doc/Documentation/networking/scaling.txt) from Linux kernel documentation briefly describes these technologies:

  * **[RSS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/performance_tuning_guide/network-rss)**: Receive Side Scaling
  * **[RPS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/performance_tuning_guide/network-rps)**: Receive Packet Steering
  * **[RFS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/performance_tuning_guide/network-rfs)**: Receive Flow Steering
  * **[Accelerated Receive Flow Steering](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/performance_tuning_guide/network-acc-rfs)**
  * **XPS**: Transmit Packet Steering

Also check the [Interrupts and IRQ Tuning](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/performance_tuning_guide/s-cpu-irq) from Red Hat documentation.

## Example
  Using `ethtool` in **Ubuntu 16.04** to configure `enp175s0f0` to use only one queue for Tx and only one queue for Rx:

```bash
# number of queues
ethtool -L enp175s0f0 combined 2
# XPS flow direction
# mask ffff,ffffffff for 48 cores
echo ffff,ffffffff > /sys/class/net/enp175s0f0/queues/tx-0/xps_cpus
echo 0 > /sys/class/net/enp175s0f0/queues/tx-1/xps_cpus
# ntuple and filter rule
# grep Filter first to avoid adding the same rule twice
if ! ethtool -u enp175s0f0 | grep Filter; then
  ethtool -K enp175s0f0 ntuple on
  ethtool -U enp175s0f0 flow-type udp4 action 1
fi
```

Configure the kernel before tuning IRQ affinity in this situation:

```bash
echo -1 > /proc/sys/kernel/sched_rt_runtime_us
echo never >/sys/kernel/mm/transparent_hugepage/enabled
echo 0 > /proc/sys/kernel/numa_balancing
service irqbalance stop
```

IRQ number can be acquired this way:

```bash
$ cat /proc/interrupts | grep enp175s0f0
 212:          6          0          0         82          0 2437587486          0     158890          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0  IR-PCI-MSI 91750400-edge      enp175s0f0-TxRx-0
 213:          0          5          0         60          0          0          0 2437487132          0     116429          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0  IR-PCI-MSI 91750401-edge      enp175s0f0-TxRx-1
 214:          0          4          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0          0  IR-PCI-MSI 91750402-edge      enp175s0f0
```

Configure IRQ affinity to assign each queue to a dedicated core and move all the other IRQ to other cores:

```bash
echo 7 > /proc/irq/212/smp_affinity_list #queue #1 (Tx) to core #7
echo 9 > /proc/irq/213/smp_affinity_list #queue #2 (Rx) to core #9
```


## References
[1] Scaling in the Linux Networking Stack
([https://www.kernel.org/doc/Documentation/networking/scaling.txt](https://www.kernel.org/doc/Documentation/networking/scaling.txt))  
[2] Pushing the Limits of Kernel Networking([https://rhelblog.redhat.com/2015/09/29/pushing-the-limits-of-kernel-networking/](https://rhelblog.redhat.com/2015/09/29/pushing-the-limits-of-kernel-networking/))  
[3] Interrupts and IRQ Tuning([https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/performance_tuning_guide/s-cpu-irq](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/performance_tuning_guide/s-cpu-irq))  