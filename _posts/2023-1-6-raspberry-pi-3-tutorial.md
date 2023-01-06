---
layout: post
title:  "Using the Raspberry Pi 3 with Fedora, Raspian and Ubuntu: Getting Started"
date:   2023-1-06 1:03:00 -0700
categories: tutorials
---

## Motivation
This is a tutorial to get the Raspberry Pi (`rpi`) beginner up and running with the device using Linux OS.

More tutorials will follow on this blog.

## Tutorial Overview
> This tutorial focuses on a Raspberry Pi 3. We will install Raspian, Ubuntu. We will do so interfacing with a Fedora Linux laptop.


## Hardware / Requirements

> Things you'll need:
- Wifi:
    - `rpi` (Raspberry Pi) hardware version `>=3`, e.g. 3 or greater, has built in wifi.
    - Any older versions will require a dongle.
- HDMI cable
- Ethernet cable
- Extra mouse and keyboard for the rpi device
- SDCard slot on your laptop, or an SDCard adapter for your laptop.
    - If you have a Mac, you'll probably need this adapter.
    - This tutorial is generally for Linux developers.
- SDCard and adapter that is compatible with your rpi.

## Operating Systems for the rpi
The rpi3 is 64-bit, but the benefits are limited for using 32-bit.

### Ubuntu rpi compatibility
Ubuntu keeps a downloads page [here](https://ubuntu.com/download/raspberry-pi).

> Some relevant notes about Ubuntu:
- Ubuntu Desktop, as of now, only works on rpi 4+ due to requirements.
- Ubuntu Server will require a desktop. 
    - See https://www.makeuseof.com/install-desktop-environment-gui-ubuntu-server/ to choose and install a desktop.
    - Installation instructions here: https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#3-using-advanced-options
- Ubuntu Core will require [registration with Ubuntu](https://ubuntu.com/download/raspberry-pi-core).
    - Ubuntu Core is a great OS of choice for embedded systems development.
    - See information about Ubuntu Core here: https://www.youtube.com/watch?v=aekZhezFCHM

> For the rpi 3, Ubuntu Server 22.10 is compatible.

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
9. Profit.

## Installing with Raspian
TODO