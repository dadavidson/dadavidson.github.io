---
layout: post
title: "How to Enable Web Service on a Cisco Catalyst"
date: 2022-06-18 11:30:00
style: border
color: primary
tags: [Networking]
description: Enabling a managed web service on Cisco switches
---

Cisco Catalyst switches include remote configuration options such as connecting through telnet, SSH and web interface. If you are configuring a Cisco Catalyst switch and would like to manage it from a web browser and be able to check traffic insights keep reading.

## Step 1

Open a terminal, PuTTT, or whatever ssh/telnet/console client works for you.

## Step 2

SSH into the switch

```
ssh x.x.x.x
```

Substitute the IP address of the Cisco Catalyst switch for the "x.x.x.x," and then press the "Enter" key.

## Step 3

Type the password when requested to connect to the Cisco Catalyst.

## Step 4

Type `enable` and press the "Enter" key. Type the Privileged Exec password when requested, and press the "Enter" key.

## Step 5

Type the following commands:
```
Switch# config t
Switch(config)#
Switch(config)# ip http server
Switch#
```

## Step 6

Don't forget to save your changes:

```
Switch# copy running-config to startup-config
```

## Step 7

Open a web browser and go to the IP address or hostname of your switch

![](https://i.imgur.com/5z07Fli.jpg)