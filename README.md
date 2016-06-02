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

Unmount the SD Card cleanly, and re-insert it in the ACME system them power it back.

Run a standard SSH connection to get a system shell :
```
$: ssh root@baylibre-acme.local
```

## CLI Usage Instructions ##

The experimenal Network CLI is available at https://github.com/baylibre-acme/acme-cli

The pyacmed server is provided in the lastest ACME Yocto based images and works with this CLI software.
