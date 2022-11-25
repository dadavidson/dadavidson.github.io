---
layout: post
title: "Get Pip for Python2"
date: 2021-11-26 01:30:00
tags: [Coding, Python, Tips]
style: border
color: primary
description: How-To Install Pip for Python 2
---

![](https://i.imgur.com/BlsZ8VG.png)

Recently I've ran across an issue where many of the exploits from [exploit-db](https://www.exploit-db.com/) are coded in Python2. That said, many of the dependencies/modules also require that they be installed via pip2. While you can translate these scripts into Python3 there is a simpler way.

With Python2 replaced by Python3 this presents a challenge to acquire pip2. Therefore, in this tutorial, I will show you how you can quickly solve this issue and install pip2 on your system to get on with your business and continue your exploits/developments.

---

## Install Python 2 on Linux

`sudo apt install python2`

OR

`sudo apt install python-minimal`

## Download the get-pip.py Script

`wget https://bootstrap.pypa.io/pip/2.7/get-pip.py`

## Install

`sudo python2 get-pip.py`

## Install modules

*Note: This is optional. Just showcasing how to install a module under pip2.*

`/usr/local/bin/pip2 install paramiko`
