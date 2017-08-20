---
layout:            post
title:             "Time Synchronization with GPS in Ubuntu"
date:              2016-08-14
tags:              NTP GPS PPS
category:          Others
author:            hongyuan

---

This post will walk you through the procedure of synchronizing your PC (running Ubuntu) clock with a GPS device. I'm assuming that you have a GPS device with **RS232** output (because this was the only device that I got). Your system time can be synchronized to **UTC** within a few microseconds if your device also supports **PPS**.

## Introduction
**1. GPS output interface**{:.em-uln}

The following contents are copied from [this article](https://www.lammertbies.nl/comm/info/GPS-time.html):

>There are many cheap GPS receivers available in the market. Most of them use either an [**RS232**](https://www.lammertbies.nl/comm/cable/RS-232.html), or [**USB**](https://www.lammertbies.nl/comm/info/usb-specification.html) connection to send their information to the attached computer. Although the clock inside the receiver itself runs with an accuracy of some nanoseconds, the transfer of the data to the computer causes such a large delay, that in practice it is not possible to synchronize the clock of the local computer with that signal with an accuracy of better than a handful of milliseconds. That kind of accuracy can also be obtained by connecting to a freely available NTP time server over the internet. Only GPS devices which have a special fast and accurate synchronization method with the computer can be used as a time synchronization device. The most expensive and accurate way to do this is to use a GPS receiver which fits in a local **PCI** or **PCIex** slot of the computer. But these cards are very expensive and not widely available. The other solution is to use the slow and inaccurate **RS232** or **USB** interface to send general data and do the time synchronization with a pulse. The rising or falling edge of that pulse can then be used for the synchronization of the time inside the computer.
>
>Often accuracies within microseconds are possible with accepting pulses through the serial interrupt system. On most GPS devices with pulse capability, the pulse is sent once every second, starting at the beginning of every new second. This is why these GPS devices are often referred to as GPS with **PPS**, for pulse per second.

**2. About NTP**{:.em-uln}

**NTP** stands for **Network Time Protocol**. It is a networking protocol for clock synchronization between computer systems over packet-switched, variable-latency data networks. In this post we use NTP to synchronize our system time with a GPS device (a reference clock). You can read more about NTP from its [wiki page](https://en.wikipedia.org/wiki/Network_Time_Protocol).

**3. Procedures**{:.em-uln}

* Install prerequisites
* Connect your GPS device to a serial port of your PC
* Verify that the signals (NMEA string/PPS) emitted by your GPS can be detected by your PC
* Build NTP with PPS support
* Configure and calibrate NTP
* Enjoy!

## Install prerequisites
```bash
sudo apt-get update
sudo apt-get install pps-tools     # PPS kernel support
sudo apt-get install setserial
sudo apt-get install util-linux    # obtain ldattach
```

## Connect GPS to your PC
If your GPS device already has a **RS232 DE9** interface then all you have to do is to connect it to one of your PC's serial port. If, unfortunately there's no **RS232 DE9** interface, you may have to wire them manually. Take a look at [this post](http://www.rjsystems.nl/en/2100-ntpd-garmin-gps-18-lvc-gpsd.php) about how to wire different pins.

Also make sure that your GPS antenna can receive signal from satellites properly.

## Test GPS output
In Linux system, all the physical serial ports are mapped into a file under the directory /dev, named **ttyS** and ended with a number. For example, /dev/ttyS0 is the first serial port (COM1) on your machine and /dev/ttyS1 is the second (COM2). In this section, we can check the GPS output simply by examining the files under /dev.

**1. The NMEA string**{:.em-uln}

Use a symbolic link to bind your GPS device (<span style="color: red">make sure you use the correct COM port number</span>):

```bash
sudo ln -s /dev/ttyS0 /dev/gps1 		# ttyS0 for COM1
```

Then check that your PC can receive the data from it:

```bash
cat /dev/gps1
```

In my case, the GPS is emiting GPZDA signals so I can see something like this:

```
$GPZDA,165236.000,12,11,2015,,*56
$GPZDA,165237.000,12,11,2015,,*57
$GPZDA,165238.000,12,11,2015,,*58
$GPZDA,165239.000,12,11,2015,,*59
$GPZDA,165240.000,12,11,2015,,*57
$GPZDA,165241.000,12,11,2015,,*56
$GPZDA,165242.000,12,11,2015,,*55
```

**2. The PPS**{:.em-uln}

_Ldattach_ (a tool from util-linux) can attach a line discipline to a serial line to allow for in-kernel processing of the received and/or sent data. We can use it to attach the PPS signal from RS232 for kernel processing  (<span style="color: red">make sure you are entering the correct COM port number!</span> In my case, the PPS signal comes from **Pin1** of the same **RS232 DE9** interface so the COM number is the same as the one for the NMEA signal):

```bash
sudo modprobe 8250
sudo ldattach PPS /dev/ttyS0		# ttyS0 for COM1
```

This will create a file _**/dev/pps0**_. Now, let's test whether our system has received the PPS signal:

```bash
sudo ppstest /dev/pps0
```

If successful, time stamp data will be displayed every second:

```
trying PPS source "/dev/pps0"
found PPS source "/dev/pps0"
ok, found 1 source(s), now start fetching data...
source 0 - assert 1554600619.010236333, sequence: 3700 - clear  1554600618.110688977, sequence: 4858
source 0 - assert 1554600619.010236333, sequence: 3700 - clear  1554600619.110666464, sequence: 4859
source 0 - assert 1554600620.010234952, sequence: 3701 - clear  1554600619.110666464, sequence: 4859
source 0 - assert 1554600620.010234952, sequence: 3701 - clear  1554600620.110685220, sequence: 4860
source 0 - assert 1554600621.010235825, sequence: 3702 - clear  1554600620.110685220, sequence: 4860
source 0 - assert 1554600621.010235825, sequence: 3702 - clear  1554600621.110694069, sequence: 4861
```

To detach discipline:

```bash
sudo pkill -f ldattach
```

## Build NTP with PPS support
Although you can get NTP easily by apt-get. It doesn't come with PPS support by default, so we have to build NTP manually with all clocks enabled. But before that, we need to make sure a header file called _**timepps.h**_ (already installed by `sudo apt-get install pps-tools`) can be found by NTP:

```bash
sudo cp /usr/include/sys/timepps.h /usr/include/
```

Now download and build NTP:

```bash
mkdir ntp
cd ntp
wget http://archive.ntp.org/ntp4/ntp-4.2/ntp-4.2.6p5.tar.gz --no-check-certificate
tar -xf ntp-4.2.6p5.tar.gz
cd ntp-4.2.6p5
./configure --enable-all-clocks
make
sudo make install
```
It is recommended that you check the output of _**./configure --enable-all-clocks**_ and make sure it says <span style="color: red">'checking for timepps.h... yes'</span>.

## Configure & Calibrate NTP
**1. Configuration**{:.em-uln}

First, we need to enable 'low_latency' support for the serial driver. This greatly reduces observed jitter on the ref clock. If this is not enabled, ntpd may reject the ref clock as unstable. To do so:

```bash
sudo setserial /dev/ttyS0 low_latency
```

Now create a config file (/etc/ntp.conf) for NTP:

```bash
sudo gedit /etc/ntp.conf
```

Edit this file to include the address of your ref clock and PPS support, also the driftfile location (below is just an example):

```
# Time reference 1: type 20 (NMEA), unit 1 (/dev/gps1)
#   mode 8 = \$GPZDA or \$GPZDG @ 4800 bps
#   time2 = serial end of line time offset
server 127.127.20.1 mode 8 prefer
fudge 127.127.20.1 time2 0.090

# Time reference 2: type 22 (PPS), unit 0 (/dev/pps0)
#   minpoll = use 16-second sampling
#   flag2 = capture on the falling edge
#   flag3 = use the kernel discipline
server 127.127.22.0 minpoll 4 maxpoll 4
fudge 127.127.22.0 flag2 1 flag3 1

# Driftfile location
driftfile /etc/ntp.drift
```

Detailed documentation of configuring NTP can be found at [this link](http://http://support.ntp.org/bin/view/Support/ConfiguringNTP), here we will only talk about something that I think is essential in understanding this example:

* **The 'IP address'**  
In this example, two servers (time references) were added by specifying thier IP addresses. Radio and modem clocks by convention have addresses in the form 127.127.t.u, where t is the clock type and u is a unit number in the range 0-3 used to distinguish multiple instances of clocks of the same type. For example, 127.127.20.1 means type 20, unit 1, where type 20 is the _Generic NMEA GPS Receiver_ and unit 1 corresponds to /dev/gps1 in Linux; 127.127.22.0 means type 22, unit 0, where type 22 means PPS Clock Discipline and unit 0 corresponds to /dev/pps0.  
All types of reference clocks and their codes can be found [here](http://doc.ntp.org/4.1.2/refclock.htm).

* **The 'mode'**  
The 'mode' byte tells NTPD what GPS sentences and bitrates to look for. It has 16bits where the first for bits are for GPS sentence and bits 4-6 are for baud rate. In our example, mode 8 means receiving $GPZDA or $GPZDG at 4800 bps. Deatils can be found at [this link](https://www.eecis.udel.edu/~mills/ntp/html/drivers/driver20.html).

* **The 'time2'**  
NTPD assumes the end of the GPZDA message is aligned with the second edge.  On the Syncbox, the start of the message is aligned with the second edge.  If this offset is too high, NTPD rejects the ref clock. So we need to add an offset between them. 90 msec is the number used in [this tutorial](http://www.worldtimesolutions.com/support/ntp/Debian_Lenny_Linux_PPS_support_for_ntpd.html) from WorldTime Solution.

* **The 'flags'**  
The flags are mainly for controlling PPS signal processing. Details can be found [here](https://www.eecis.udel.edu/~mills/ntp/html/drivers/driver20.html).


**2. Calibration**{:.em-uln}

Start NTPD and use the -g option to allow the first adjustment to be Big (in the case of unsynced GPS, e.g. indoor):

```bash
sudo ntpd -g
```

Now check if NTPD is synchronised with your GPS:

```bash
ntpq -pn
```

You should see something like this:

```
      remote           refid      st t when poll reach   delay   offset  jitter
===============================================================================
*127.127.20.1     .GPS.            0 l   35   64    3    0.000   -13.771  2.162
o127.127.22.0     .PPS.            0 l    2   16   63    0.000   41.939  55.193
```

Explanations of the fields are as below:

* **remote**: peers speficified in the ntp.conf file
  * \* = current time source
  * o = source selected, PPS used
  * \# = too distant
  * \+ = selected
  * x = false ticker
  * \- = discarded
* **st**: stratum level of the source
* **t**: types available
  * l = local
  * u = unicast
* **when**: number of seconds passed since last response
* **poll**: polling interval, in seconds, for source
* **reach**: indicates success/failure to reach source, 377 all attempts successful
* **delay**: indicates the roundtrip time, in milliseconds, to receive a reply
* **offset**: indicates the time difference, in milliseconds, between the client server and source
* **jitter**: indicates the difference, in milliseconds, between two samples

Detailed explanations of the output can be found [here](http://tech.kulish.com/2007/10/30/ntp-ntpq-output-explained/).

If instead of an ‘o’ or ‘*’ you get two ‘x’ here, that’s ok for now. It just indicates that NTP wont use any of them as a time source. For the sake of calibration, we just want to know the approximate difference in offsets. The important part here is the value for ‘reach’ indicating it’s communicating and it is updating ‘offset’ for both sources.

Remember the parameter '**time2**' we set in the configuration file? This is the offset value that we want to find about our device. In the above example, ntpq gave the offsets between both of the reference clocks and the system clock. So we can calculate the '**time2**' as `41.939 - (-13.771) = 55.71 mSec = 0.05571 Sec`. Replace the value in /etc/ntp.conf so that you have something like `fudge 127.127.20.1 time2 0.05571` and then restart NTPD and wait for it to lock after which you should see the offsets have become much smaller. Then just repeat this procedure for a couple of times until you are happy with the offset and jitter of your PPS reference.



## How to Restart NTPD after Reboot
```
SERIAL=/dev/ttyS0                        # choose the COM port
sudo modprobe 8250
sudo ldattach PPS ${SERIAL}              # create /dev/pps0
sudo ln -s ${SERIAL} /dev/gps1           # create /dev/gps1
sudo setserial ${SERIAL} low_latency
sudo ntpd -g                             # start NTPD
ntpq -pn                                 # check output
```

## References
\[1] GPSD Time Service HOWTO ([http://catb.org/gpsd/gpsd-time-service-howto.html](http://catb.org/gpsd/gpsd-time-service-howto.html))  
\[2] Stratum-1 NTP Server ([http://morcant.net/blog/?page_id=26](http://morcant.net/blog/?page_id=26))  
\[3] Enabling ntpd PPS support for Debian Lenny Linux ([http://www.worldtimesolutions.com/support/ntp/Debian_Lenny_Linux_PPS_support_for_ntpd.html](http://www.worldtimesolutions.com/support/ntp/Debian_Lenny_Linux_PPS_support_for_ntpd.html))
