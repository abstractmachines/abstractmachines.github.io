---
layout: post
title:  "Tutorial: Building WPE WebKit for Raspberry Pi 3"
date:   2023-1-17 1:03:00 -0700
categories: igalia
---

> A lightning guide for building WPE WebKit with buildroot

This tutorial will be for getting "up and running" with WPE WebKit using a Raspberry Pi 3 using a laptop/desktop with Linux Fedora installed. WPE WebKit has many benefits; you may [read here about why WPE WebKit is a great choice for embedded devices](https://wpewebkit.org/about/a-good-choice.html). WPE WebKit has minimal dependencies and it displays high-quality animations, WebGL and videos on embedded devices.

[WebPlatformForEmbedded](https://github.com/WebPlatformForEmbedded) is our focus; for this tutorial, we'll be building WPE WebKit with buildroot to build our image, so, make sure to [clone the buildroot repository](https://github.com/WebPlatformForEmbedded/buildroot).

## You will need:

> Raspberry pi 3 items:

- A raspberry pi 3.
- A microSD card for the pi. (I usually choose 32GB microSD cards).
- An external monitor, extra mouse, and extra keyboard for our rpi3, separately from the laptop.
- An HDMI cable to connect the rpi3 to its own external monitor.

> Laptop items:

- Linux laptop/desktop.
  - This tutorial will be based on Fedora, but you can use Debian or any distro of your choice.
- You also need a way to interface the microSD card with your laptop. You can get an SD Adapter for laptops that have an SD Card adapter slot, or you can use an external SD Card adapter interface for your computer.
  - This tutorial will be based on having a laptop with an SD card slot, and hence an SD Card Adapter will work just fine.

> Items for laptop to communicate with rpi3:

- An ethernet cable to connect the rpi3 to your laptop.
- You need some way to get ethernet into your laptop. This is either in the form of an ethernet port on your laptop (not likely), or an adapter of some sort (likely a USB adapter).

## Steps: High level overview

> This is a high level overview of the steps we will be taking.

1. Partition the blank SD card. 
2. In the `buildroot` repository, `make <desired_config>`.
3. Run the `buildroot` `menuconfig` with `make menuconfig` to set up `.config` file.
4. Run `make` to build `sdcard.img` in `buildroot/output` dir; change `.config` settings as needed.
5. Write `sdcard.img file` to the SD card.
6. Connect the rpi3 to its own external monitor, and its own mouse and keyboard.
7. Connect the rpi3 to the laptop using ethernet cable.
8. Put the SD card into the rpi3.
9. Setup a shared ethernet connection between the laptop and rpi to get the IP address of rpi.
10. ssh into the rpi and start WPE WebKit.

## Steps: Detailed overview/sequence

### 1. Partition the blank SD card using `fdisk`. Create a boot partition and a root partition.
- Note: this is only needed in case the output image format is root.tar. If it's sdcard.img, then that is dumped directly to the sdcard, that image is already partitioned internally.
- If you're unfamiliar with fdisk, [this is a good tutorial](https://www.nayab.xyz/linux-tools/partitioning-using-fdisk).


### 2. In the `buildroot` repository root directory, `make` the desired config.

- Make sure to [clone the buildroot repository](https://github.com/WebPlatformForEmbedded/buildroot).
- Since things change a lot over time, it's important to note the specific [buildroot commit](https://github.com/WebPlatformForEmbedded/buildroot/commit/ce9dd9a89a90a50920ca34e2af4a185947123390) this tutorial was built on, and that this tutorial was built on January 12th, 2023. It is recommended to build from that commit for consistency to ensure that the tutorial works for you.
- We are building the `cog` `2.28` wpe with buildroot. See the build options from the [buildroot repository](https://github.com/WebPlatformForEmbedded/buildroot).
- Run `make list-defconfigs` to get a list of configurations.
- Copy `raspberrypi3_wpe_2_28_cog_defconfig` and run it: `make raspberrypi3_wpe_2_28_cog_defconfig`.
- You will quickly get output which indicates that a `.config` file has been written in the root directory of the buildroot repository.

### 3. Run the `buildroot` `menuconfig` with `make menuconfig` to set up `.config` file.

- Run `make menuconfig`. You'll see options here for configuration. Go slowly and be careful.
- Change these settings. Help menus are available for `menuconfig`, you'll see them displayed on the screen.

| Operation in menuconfig | Location | Value |
| ----------- | ----------- | ----------- |
| ENABLE | Target packages -> Filesystem and flash utilities | dosfstools |
| ENABLE | Target packages -> Filesystem and flash utilities | mtools |
| ENABLE | Filesystem images | ext2/3/4 root filesystem |
| SET VALUE | Filesystem images -> ext2/3/4 root filesystem -> ext2/3/4 variant | ext4 |
| DISABLE | Filesystem images | initial RAM filesystem linked into linux kernel |


### 4. Run `make` to build `sdcard.img` in `buildroot/output` dir; change `.config` settings as needed.
- Run `make`. Then get a coffee as the build and cross-compilation will take awhile.

- In reality, you may encounter some errors along the way, as cross-compilation can be an intricate matter.
This tutorial will guide you through those potential errors.
- When you encounter errors, you'll follow a "loop" of sorts:
>> Run `make` -> encounter errors -> manually edit `.config` file -> -> remove `buildroot/output` dir -> run `make` again until `sdcard.img` is built successfully.

- If you encounter CMake errors, such as `fatal error: stdlib.h: No such file or directory, compilation terminated`, and you have a relatively new version of CMake on your system, the reason for the error may be that buildroot is using your local CMake instead of the one specified in the buildroot configuration.
  - We will fix this error by setting in `.config` file: `BR2_FORCE_HOST_BUILD=y`. Then remove `buildroot/output` dir, and run `make` again.
 - If you encounter error such as `path/to/buildroot/output/host/lib/gcc/arm-buildroot-linux-gnueabihf/9.2.0/plugin/include/builtins.h:23:10: fatal error: mpc.h: No such file or directory`:
  - then we can fix this error by changing Makefile in `./output/build/linux-rpi-5.10.y/scripts/gcc-plugins/Makefile`, by adding  `-I path/to/buildroot/output/host/include` in `plugin_cxxflags` stanza. Then, as usual, remove `buildroot/output` dir, and run `make` again.


### 5. Write `sdcard.img file` to the SD card.
- At this point after the `make` process, we should have `sdcard.img` file in `buildroot/output/images` directory.
- Write this file to the SD card.
- Consider using Etcher to do so.


### 6. Connect the rpi3 to its own external monitor, and its own mouse and keyboard.
- We'll have separate monitor, mouse and keyboard all connected to the raspberry pi so that we can use it independently from the laptop.

### 7. Connect the rpi3 to the laptop using ethernet cable.

### 8. Put the SD card into the rpi3.

### 9. Setup a shared ethernet connection between the laptop and rpi to get the IP address of rpi.
In general, one of the main problems for connecting via ssh to the Raspberry Pi is to know the IP address of the device.
This is very simple with Raspbian OS; simply turn on the raspberry pi and edit configurations to enable ssh, often over wifi.

This is where the ethernet capabilities of the raspberry pi come in.
> Goal: To find syslog message `DHCPACK` acknowledgement and assignment of the IP address after setting up shared connection between raspberry pi and the laptop.

>> Throughout this process, continually look at logs. Eventually we will see a message DHCPACK which will likely be preceded by several DHCP handshake related messages such as DHCP DISCOVER, REQUEST etc. The DHCPACK message will contain the IP address of the ethernet device, and we will then be able to ssh into it.

- 1. Tail the syslogs of the laptop. On Debian distributions, this is often `/var/log/syslog`. Since we are using Fedora, we'll be using `systemd's` `journald` with the `journactl` command:
  - `sudo journalctl -f`
  - Keep this open in a terminal window.
  - You can also come up with a better solution like grepping logs, if you like, or piping output of stdout elsewhere.
- 2. In a second terminal window, open up the NetworkManager.
  - Become familiar with existing devices prior to powering on the raspberry pi, by running `nmcli`.
- 3. Power on the raspberry pi. Watch your system logs.
  - Syslogs will detail the raspberry pi's `name`.
- 4. Look for that `name` in NetworkManager `nmcli device`.
  - Using NetworkManager `nmcli`, set up shared connection for the ethernet device.
  - Setting up a shared connection is as simple as `nmcli connection add type ethernet ifname $ETHERNET_DEVICE_NAME ipv4.method shared con-name local`
   - [This is a good tutorial](https://fedoramagazine.org/internet-connection-sharing-networkmanager/) for setting up a shared connection with NetworkManager.
- 5. Once the connection is shared, syslogs will show a `DHCPACK` message acknowledging the ethernet device and its IP address. (You may need to power cycle the rpi to see this message, but it will happen).
  

### 10. ssh into the rpi and start WPE WebKit.
- Now that we have the IP address of the raspberry pi, we can ssh into it from the laptop: `ssh root@<RPI3_IP_ADDRESS>`. (The default password is 'root'. You can also add your user public key to /root/.ssh/authorized_keys on the pi. You can simplify this process by creating an overlay/root/.ssh/authorized_keys on your computer and by specifying the path to the overlay directory in the BR2_ROOTFS_OVERLAY config variable. That will copy everything in the overlay dir to the image.)
- After that, export these env variables `WPE_BCMRPI_TOUCH=1` and `WPE_BCMRPI_CURSOR=1` to enable keyboard and mouse control. 
  - _Why:_ Recall that generally WPE WebKit is for embedded devices, such as kioks, or set top boxes requiring control with a remote control or similar device or touch interaction. We are exporting these environment variables so that we can "test" WPE WebKit with our separate mouse and keyboard for our raspberry pi without the need for a touch screen or special hardware targets, or a Wayland compositor such as `weston`. If this piques your curiosity, please see the [WPE WebKit FAQ on Wayland](https://wpewebkit.org/about/faq.html#is-wayland-required-to-run-wpe%3F).
- Start WPE WebKit with `cog`: `cog "http://www.igalia.com/"`
- A browser will launch in the external monitor connected to the raspberry pi 3, and we can control the browser with the raspberry pi's mouse and keyboard!


That's all for now. Feel free to reach out in the support channels for WPE [on Matrix](https://matrix.to/#/#wpe:matrix.org).

# WPE's Frequently Asked Questions
- [https://wpewebkit.org/about/faq.html](https://wpewebkit.org/about/faq.html)
