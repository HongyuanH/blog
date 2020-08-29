---
layout:            post
title:             "Install Ubuntu on Raspberry Pi 4"
date:              2020-06-13
tags:              Pi
category:          Pi
author:            hongyuan

---

## Where to start?

[Click here for the official tutorial](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#1-overview).

## Or just follow these steps:

#### 1. Download Raspberry Pi Imager, depending on which OS your are using on your PC:

[Click here if you are running Ubuntu](https://downloads.raspberrypi.org/imager/imager_amd64.deb)

[Click here if you are running Windows](https://downloads.raspberrypi.org/imager/imager.exe)

[Click here if you are running macOS](https://downloads.raspberrypi.org/imager/imager.dmg)

Install it on your PC after downloading.

##### 2. Download Ubuntu iamge for Raspberry Pi:

Download the Ubuntu image you want from [here](https://ubuntu.com/download/raspberry-pi).

Ubuntu 20 or 18?

It seems Pi 4 doesn't work well with **Ubuntu Core 18** for some reasons. I saw the `start4.elf: is not compatible` error when booting Raspberry Pi with **Ubuntu Core 18**, so I chose to install **Ubuntu 20** instead.

#### 3. Write the Ubuntu image into SD card:

Insert the SD card into your PC. Open **Raspberry Pi Imager**. Click `CHOOSE SD CARD` to select the SD card. Click `CHOOSE OS` -> `Use custom` and locate the Ubuntu image you just downloaded. Click `WRITE`.

#### 4. WIFI:

Open the partition created during step 3 and edit the `network-config` file to add your Wi-Fi credentials. Below is an example of how I configured WIFI with static IP address for my Pi:

```
wifis:
  wlan0:
    dhcp4: false
    dhcp6: false
    optional: true
    access-points:
      "${WIFINAME}":
        password: "${WIFIPASSWD}"
    addresses:
      - 192.168.0.200/24
    gateway4: 192.168.0.1
    nameservers:
      addresses: [192.168.0.1, 8.8.8.8]
```

During the first boot, your Raspberry Pi will fail to connect to WIFI the first time around. Simply reboot sudo reboot and it will work.

## Troubleshoot 

#### Can't see the "system-boot" partition?

In my Windows PC the partition was shown as `RECOVERY` instead of `system-boot` after loading the image into SD card.

#### Stuck on rainbow colour image?

I also found the grenn LED next to the power supply blinking 7 times which means the file `kernel.img` was not found on SD card. I personally didn't know why this happened but reburning the SD card solved the problem.

#### Can't login into Pi?

I got the `Incorrect login` error after booting Pi. Turns out Pi needs some extra time to boot even when you can already see the login prompt. Wait for one or two minutes before trying again.

#### Can't connect to WIFI?

I got this error when trying to configure WIFI:

```
ubuntu@ubuntu:~$ sudo netplan apply
Failed to start netplan-wpa-wlan0.service: Unit netplan-wpa-wlan0.service not found.
Traceback (most recent call last):
  File "/usr/sbin/netplan", line 23, in <module>
    netplan.main()
  File "/usr/share/netplan/netplan/cli/core.py", line 50, in main
    self.run_command()
  File "/usr/share/netplan/netplan/cli/utils.py", line 179, in run_command
    self.func()
  File "/usr/share/netplan/netplan/cli/commands/apply.py", line 46, in run
    self.run_command()
  File "/usr/share/netplan/netplan/cli/utils.py", line 179, in run_command
    self.func()
  File "/usr/share/netplan/netplan/cli/commands/apply.py", line 173, in command_apply
    utils.systemctl_networkd('start', sync=sync, extra_services=netplan_wpa)
  File "/usr/share/netplan/netplan/cli/utils.py", line 86, in systemctl_networkd
    subprocess.check_call(command)
  File "/usr/lib/python3.8/subprocess.py", line 364, in check_call
    raise CalledProcessError(retcode, cmd)
subprocess.CalledProcessError: Command '['systemctl', 'start', '--no-block', 'systemd-networkd.service', 'netplan-wpa-wlan0.service']' returned non-zero exit status 5
```

It's an known bug for **ubuntu-20.04-preinstalled-server-arm64+raspi**, here's how to fix it:

* Write the following to cloud.cfg.d:

```echo 'network: {config: disabled}' | sudo tee  /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg```

* Configure netplan as usual, link to tutorial below: 

[https://netplan.io/examples](https://netplan.io/examples)

* Apply & reboot (ignore the errors you see while doing `netplan apply` and just reboot):

```
sudo netplan generate
sudo netplan apply
sudo reboot
```

Reference: [https://raspberrypi.stackexchange.com/a/111787](https://raspberrypi.stackexchange.com/a/111787)