---
layout: post
title:  "Getting started with Raspberry Pi 3: choosing an OS"
date:   2023-1-06 1:03:00 -0700
categories: tutorials
category: raspberrypi
---

## Motivation
This is a tutorial to get the Raspberry Pi (`rpi`) beginner up and running with the device using Linux.

We will cover installing an operating system on the rpi; the operating systems we'll cover will include `Ubuntu Server` and `Raspbian OS`.

For the Ubuntu portion, a lot of this tutorial is similar to (and taken from) this tutorial [https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#4-boot-ubuntu-server), but I've added more details where rpi beginners may get stuck.

## Tutorial Overview
> This tutorial focuses on a Raspberry Pi 3. We will install Raspian, Ubuntu. We will do so interfacing with a laptop with Fedora installed, so we'll be using the `dnf` package manager.

## Hardware / Requirements

> Things you'll need:

- WiFi:
    - `rpi` (Raspberry Pi) hardware version `>=3`, e.g. 3 or greater, has built in wifi.
    - Any older versions will require a dongle.
    - A WiFi connection
- HDMI cable
- Extra mouse and keyboard for the rpi device
- SDCard slot on your laptop, or an SDCard adapter for your laptop.
    - If you have a Mac, you'll probably need this adapter.
    - This tutorial is generally for Linux developers.
- SDCard and adapter that is compatible with your rpi.

## Operating Systems for the rpi
The rpi3 is 64-bit, but the benefits of using 64-bit on the rpi3 are limited due to hardware restrictions.

### Ubuntu rpi compatibility
Ubuntu keeps a downloads page [here](https://ubuntu.com/download/raspberry-pi); this page has compatibility information.

> Some relevant notes about Ubuntu and the rpi3:

- Ubuntu Desktop, as of now, only works on rpi 4+ due to requirements.
- Ubuntu Server will require a desktop. 
    - See [https://www.makeuseof.com/install-desktop-environment-gui-ubuntu-server/](https://www.makeuseof.com/install-desktop-environment-gui-ubuntu-server/) to choose and install a desktop.
    - Installation instructions here: [https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#3-using-advanced-options](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#3-using-advanced-options)
- Ubuntu Core will require [registration with Ubuntu](https://ubuntu.com/download/raspberry-pi-core).
    - Ubuntu Core is a great OS of choice for embedded systems development.
    - See information about Ubuntu Core here: https://www.youtube.com/watch?v=aekZhezFCHM

>> For the rpi 3, Ubuntu Server 22.10 is compatible.

## Installing with Ubuntu Server OS

To start the process of installing Ubuntu Server on your rpi, insert an SDCard through its adapter into the SDCard slot on your laptop.

1. Use the `dnf` package manager to get the Raspberry Pi Imager.
    `sudo dnf install rpi-imager`
2. Use root permissions to launch it.
    `sudo rpi-imager`
3. `Operating System`: Choose Ubuntu Server 22.10. [This is why.](https://ubuntu.com/download/raspberry-pi).
4. `Storage`: Choose your sdcard. 
5. Configure advanced options by clicking the widget. 
    - Setup wifi SSID and password, so that rpi can connect to your wifi network.
    - Enable ssh.
6. Write. This will take a few minutes.
7. Insert the SDCard into your RPI.
8. Power it on.
    - Time to get a couple of coffees. This will take awhile.
9. You'll see a login screen, but do **NOT** login yet. Instead, wait, and you'll see some stuff print out regarding `cloud-init`.
10. _Wait_ until the `cloud-init` install "stops", and press enter.
11. You'll see a prompt:
    - `raspberry pi login:`
    - Enter the username you used during step 5 when you configured advanced options. (Usually this is `pi` on Raspbian installs and `ubuntu` on Ubuntu installs, but these are defaults, and we shouldn't be using the defaults, hence step 5). Hit enter.
    - Enter the password (you'll be prompted to do so).
    - Now we are at a CLI prompt `pi@raspberrypi:~$`

... We have now installed and logged into Ubuntu Server on RPI!

12. ssh into the raspberry pi from your laptop to establish a connection.
    - _On the rpi:_
        - We don't have `net-tools` installed (`sudo apt install net-tools` if we want it), so we'll use `hostname -I` to retrieve the hostname and the IP address.
        - Note the IP address.
    - _Then, on your laptop:_
        - `ssh pi@<rpi_ip_address>`
        - Enter password, you'll be prompted to.
    - Once we ssh into the rpi successfully, we'll see an Ubuntu welcome message.

That's all for now.
> Some followups:

- The rpi 3 is not compatible with Ubuntu Desktop. That's why we installed Ubuntu Server. Here are some articles that talk about that: [[1]](https://www.makeuseof.com/install-desktop-environment-gui-ubuntu-server/), [[2]](https://www.makeuseof.com/tag/best-linux-desktop-environments/), [[3]](https://www.makeuseof.com/tag/gnome-explained-look-one-linuxs-popular-desktops/), [[4]](https://www.tomshardware.com/how-to/install-ubuntu-raspberry-pi).

## Installing with Raspbian
_Mostly_ the same instructions as above, only you want to install Raspberry Pi _legacy_ OS, with a desktop,
as you'll see that's the OS that's compatible with rpi 3.

> The way we ssh in to an rpi with Raspbian installed will be different from Ubuntu server:

- ssh into the raspberry pi from your laptop to establish a connection.
    - _On the rpi:_
        - Run `ifconfig` on the raspberry pi terminal.
        - Note the IP address belonging to `wlan0`.
    - _Then, on your laptop:_
        - `ssh pi@<rpi_ip_address>`

## Fun tips
- To use ssh with GUI, you must enable X11 (or, one assumes, Wayland probably).
    - `ssh -X pi@<rpi_ip_address> evince /path/to/file.pdf` // or `eog /path/to/img.png`, etc

- To download files:
    - sudo scp pi@<rpi_ip_address>:/path/to/file ~/local/path/to/put/file`
    - Now that you have the file on your laptop, you may use a GNOME app to view, e.g. `evince ~/local/path/to/put/file`

## Troubleshooting
If you get:
> SSH warning: Remote host identification has changed

- Solve by removing that key for that IP from `.ssh/known_hosts`:
    - Run `ssh-keygen -R <rpi_ip_address>`
    - Then try to ssh in again.

- - - -
## Some other helpful commands and related knowledge
- Your `boot` drive will be FAT32 because that is the format required to use UEFI firmware.
    - UEFI is helpful to use in order to boot your modern device from a bootable drive easily.

    - There are differing opinions on this, but it's probably best to just use FAT32.

- Your `root` drive will be `ext4`.
    - On a legacy BIOS system, /boot can be anything understood by the bootloader, so you can use ext2, 3, or 4.
    - On EFI systems you can set /boot to ext4, ESP to vfat and add ext4 driver to the bootloader.

<span>categories: {{ page.category }}</span>
