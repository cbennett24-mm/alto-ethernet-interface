# Installation

A bunch of steps are necessary to configure the BeagleBone and install the software.
This file explains how to set up the BeagleBone and copy the files in this directory onto the BeagleBone.

## Kernel

This code requires a compatible kernel:
```
Linux beaglebone 3.8.13-bone80 #1 SMP Wed Jun 15 17:03:55 UTC 2016 armv7l GNU/Linux
```
If the BeagleBone already has a 3.8 kernel you're in luck. Otherwise, you'll need to flash a new kernel.

You can download it [here](https://debian.beagleboard.org/images/bone-debian-7.11-lxde-4gb-armhf-2016-06-15-4gb.img.xz).
To replace BeagleBone kernel, write the image to micro-SD card, boot, edit /boot/uEnv.txt to enable flash, boot to write flash, remove micro-SD card, reboot.
Detailed instructions [here](https://elinux.org/Beagleboard:BeagleBoneBlack_Debian#Flashing_eMMC) and [here](http://derekmolloy.ie/write-a-new-image-to-the-beaglebone-black/) to

## Install Mono

Connect the BeagleBone to the Internet. (e.g. connect through serial line, then dhclient eth0 for DHCP connection).
Install mono-devel: [instructions](http://www.mono-project.com/download/#download-lin-debian)
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
echo "deb http://download.mono-project.com/repo/debian wheezy main" | sudo tee /etc/apt/sources.list.d/mono-official.list
sudo apt-get update
sudo apt-get install mono-devel
```

## Software

Establish a connection to the BeagleBone, e.g. via USB. It typically will be accessible as root@192.168.7.2 (no password).
```
scp interfaces root@192.168.7.2:/etc/network/interfaces
scp IFS.tgz root@192.168.7.2:
scp capemgr root@192.168.7.2:/etc/default/capemgr
scp {gateway,ethertext.bin,etherdata.bin} root@192.168.7.2:
scp uEnv.txt root@192.168.7.2:/boot
scp PRU-ETHER-ALTO-00A0.dtbo root@192.168.7.2:/lib/firmware
scp *.service root@192.168.7.2:/lib/systemd/system
ssh root@192.168.7.2 tar xfv IFS.tgz
```

## Configure services to run on boot
Log onto the BeagleBone as root and run:

```
systemctl daemon-reload
systemctl enable alto-gateway.service
systemctl enable alto-ifs.service
systemctl start alto-gateway.service
systemctl start alto-ifs.service
```

## Reboot

Reboot the BeagleBone. After about 40 seconds, you should see two LEDs flash once a second, indicating the Breath of Life packets. If you don't have the cape connected, LED 0 will be steady on.

## Misc setup notes

You should be able to ssh into the BeagleBone as 192.168.4.5 if necessary.

If you want to use tcpdump to collect traces, install tcpdump and then:
```
tcpdump -i any -l portrange 42424-42426 -w capture.pcap
```