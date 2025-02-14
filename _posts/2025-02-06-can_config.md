---
title: CAN configuration on RPI Zero 2W
date: 2025-02-08 00:00:00 -500
categories: [programming]
tags: [programming, linux]
---

In order to config a driver add this to */boot/config.txt* file:

    dtoverlay=mcp2515-can0,oscillator=8000000,interrupt=25

Then run this

    sudo /sbin/ip link set can0 up type can bitrate 500000

Now CAN can be seen here

    ifconfig

To stop CAN if some error appears or resource is not released properly:

    sudo /sbin/ip link set can0 down