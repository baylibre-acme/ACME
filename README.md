# BayLibre ACME Yocto BSP

## Pre-built releases ##

If you need a pre-built image, please look at : https://github.com/baylibre-acme/ACME/releases

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

By default, SSH access is open to root user without any passwords.
This is bad if connected to an unsecure network.

If so, please secure the SSH settings by :
 - Copying a SSH key to connect using a key instead of a password :
```
$ ssh-copy-id root@baylibre-acme.local
```
 - Disable authentication by password :
```
$ ssh root@baylibre-acme.local
# vi /etc/ssh/sshd_config
Change lines :
#PasswordAuthentication yes
PermitEmptyPasswords yes
to
PasswordAuthentication no
PermitEmptyPasswords no 
```

## CLI Usage Instructions ##

The experimenal Network CLI is available at https://github.com/baylibre-acme/acme-cli

The pyacmed server is provided in the lastest ACME Yocto based images and works with this CLI software.

## Experimental Features ##

### USB Gadget ###

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

#### How to disable the DHCP server on the USB network interface ####

The USB network address is the same on all the ACME boards, if you connect multiple boards to a computer, you will have IP adress conflicts, to solve this situation, you can either :
 - Change the address range on each ACME BeagleBone by editing the following files :
```
# vi /usr/bin/acme-usbgadget-udhcpd
Change the line :
ifconfig usb0 up 10.65.34.1 netmask 255.255.255.0
# vi /etc/udhcpd.conf
Change the lines :
start           10.65.34.20     #default: 192.168.0.20
end             10.65.34.254    #default: 192.168.0.254
```
 - Disable the DHCP server by running :
```
# systemctl disable acme-usbgadget-udhcpd.service
```

#### How to disable the USB gadget interface ####

Simply run :
```
# systemctl disable acme-usbgadget-init.service
```

### Probe control via IIO ###

An attribute was added to the INA226 driver called "in_active", it's purpose it to control the probe power throught the IIO library.

The various utilities will be updated to handle this call.

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
# systemctl disable acme-iio-wakeup.service
```
then
```
# reboot
```
