---
layout: post
title: "How to Reset a Cisco Switch The Easy Way!"
date: 2022-05-23 06:19:00
style: border
color: primary
tags: [Networking]
description: Resetting a Cisco Switch config back to default
---

As much as I hate to admit it I've royally messed up a pretty good amount of Cisco switch configs and at times I just wanted to start over. One tiny little thing I ran into at first is just resetting the switch doesn't clear EVERYTHING!!! Those pesky VLANs stay on the switch because they are in a separate file called `vlan.dat` which is different from where the startup-config (NVRAM) resides.

Well here's how to clear a switch back to the facory defaults -- the easy way and the REALLY easy way:


## The Easy Way
```
Switch# write erase
Switch# delete flash:vlan.dat
Delete filename [vlan.dat]?
!--- Press Enter
Delete flash:/vlan.dat? [confirm]
Switch# reload
```

## The REALLY Easy Way
- Hold the *"mode"* button on the front of the switch for 10 seconds.
- The lights will blink then go solid
- The switch completely wipes all configuration and then reboots


![Mode button on a Cisco Switch Diagram](https://i.imgur.com/QnuzypE.png)