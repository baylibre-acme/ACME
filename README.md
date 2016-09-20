# BayLibre ACME Yocto BSP

## Build Instruction ##

To get the BSP you need to have repo installed and use it as:

Install the repo utility:
```
$ mkdir ~/bin
$ curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
```

Download the BSP source:
```
$ PATH=${PATH}:~/bin
$ mkdir acme-bsp
$ cd acme-bsp
$ repo init -u https://github.com/baylibre-acme/ACME
$ repo sync
```

At the end of the commands you have every metadata you need to start work with.

To start a simple image build:
```
$ make
```

An SD Card image will be available in the build/build/ directory.

## Install Instruction ##

Simply copy the image file onto the SD Card, under Linux run :
```
$ dd if=acme-bsp/build/build/sdimage-bootpart-201606021141-mmcblk.direct of=/dev/mmcblk0
```

Insert the SD Card into the BeagleBone Black slot, connect an ethernet cable to a DHCP backed LAN network and power it.
When the LEDs blinks as an heartbeat, the system is up and running.

Using a system on the same local LAN network with Avahi or Bonjour installed, test the system connectivity by pinging the ACME board :
```
$ ping baylibre-acme.local
```

## SSH Access Instructions ##

In order to achieve an SSH connection, shut off the ACME board, connect the SD Card to a computer and copy your SSH Public key as :
```
$ sudo mkdir /path/to/sdcard/root/home/root/.ssh
$ sudo cp $HOME/.ssh/id_rsa.pub /path/to/sdcard/root/home/root/.ssh/authorized_keys
$ sudo chmod 700 /path/to/sdcard/root/home/root/.ssh -R
```

Unmount the SD Card cleanly, and re-insert it in the ACME system then power it back.

Run a standard SSH connection to get a system shell :
```
$ ssh root@baylibre-acme.local
```

## CLI Usage Instructions ##

The experimenal Network CLI is available at https://github.com/baylibre-acme/acme-cli

The pyacmed server is provided in the lastest ACME Yocto based images and works with this CLI software.

## Experimental Features ##

USB Gadget support has been added to provide a network connectivity over an ECM USB profile with an embedded DHCP server and two console links over an ACM USB profile.

The freshly created network interface (usb0 under linux) will simply work with a DHCP client, and will work like a native Ethernet link.

```
$ ssh root@baylibre-acme.local
```

For the Console links, simply open a terminal emulator over one of the links, ttyACM0 and ttyACM1 under Linux.

```
$ minicom -D /dev/ttyACM0
```
or
```
$ cu -l /dev/ttyACM1
```

## How to find my ACME device on the network ##

The Acme Beaglebone Black images provides Rendez-Vous/Bonjour/Zeroconf support via the Avahi daemon.

The following are exported :
 - A entry for ipv4 and ipv6 link-local adresses
 - SSH port over the "_baylibre_acme._tcp" type
 - IIO daemon over the "_iio._tcp" type
 
In order to find the devices on the network, simply call :
```
$ avahi-browse -r _baylibre_acme._tcp
```

## How to boot with the probes disabled ##

The probe wakeup at boot can be disabled :
```
# echo 1 > /etc/acme-iio-wakeup-disable
```
or
```
# systemctl disable acme-iio-wakeup.service
```
then
```
# reboot
```
