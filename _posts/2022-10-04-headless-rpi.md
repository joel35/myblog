---
layout: post
title:  "Adventures with Headless RPi"
---

# The goal

Run a tk-inter app from Docker on a headless Raspberry Pi

# The tools

- Raspberry Pi 4
- [Raspberry Pi OS Lite 64bit](https://downloads.raspberrypi.org/raspios_lite_arm64/images/)
- 4" HDMI screen
- Docker

Install Portainer agent

    docker run -d -p 9001:9001 --name portainer_agent --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/volumes:/var/lib/docker/volumes portainer/agent:latest

# Install xorg

    sudo apt install 

# Issue 1: LCD isn't full screen
https://www.raspberrypi.com/documentation/computers/config_txt.html#hdmi-mode

    nano /boot/config.txt

Settings that seems to work for the console:

- overscan_right=48
- hdmi_force_hotplug=1
- hdmi_group=2
- hdmi_mode=14
- hdmi_drive=2
- config_hdmi_boost=4
- sdtv_mode=2

Get current screen config:

    startx
    xrandr

Errors generated in log file:

    sudo startx
    X -configure
    nano /var/log/Xorg.1.log

The answer may be in this [log file](/assets/logs/Xorg.1.log).

