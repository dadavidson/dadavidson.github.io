---
layout: post
title:  "CyberSoc | Cyber Detective CTF: Evidence Investigation"
date:   2021-09-19 22:30:00
tags: [Security, CyberSoc, OSINT]
style: border
color: primary
description: "Cyber Detective CTF: Evidence Investigation Writeup"
---

[CyberSoc Detective CTF](https://ctf.cybersoc.wales/) is a platform that contains many OSINT-based challenges. It was created by the [Cyber Society at Cardiff University](https://cybersoc.wales/about).

![](https://i.imgur.com/nJMDhzV.png)


In this post, I will be covering my write-up for the Evidence Investigation challenges. 

*NOTE: This document is subject to change without notice. I will add more solutions to the remaining challenges as I solve them.*

---

## dvla

250 points

![](https://i.imgur.com/NF23h0l.png)

Okay so they want us to find the **BRAND** and **MONTH** a vehicle was made. Given the picture below:

![](https://i.imgur.com/Db64W7p.png)

In case you didn't already know, "dvla" stands for *Driver and Vehicle Licensing Agency*. I had a little bit of an issue finding a proper site that I could input a license plate into and it return the desired output. That said, I did find two reliable ones:

- [vehicleenquiry.service.gov.uk](https://vehicleenquiry.service.gov.uk/)
- [checkcardetails.co.uk](https://www.checkcardetails.co.uk/).

![](https://i.imgur.com/wq4Vvzl.png)

Upon entering in CY10HHB...

![](https://i.imgur.com/X92bfXJ.png)

>Answer: **Ford June**.

---

## connectionrefused

300 points

![](https://i.imgur.com/gc2qYlT.png)

They mention the site([time-traveler.icec.tf](http://time-traveler.icec.tf/)) was accessible 4 years ago. Let's see if [The Wayback Machine](https://archive.org/web/) can help us go back in time and retrieve a snapshot of the website.

![](https://i.imgur.com/VwXuUnp.png)

Precisely at the June 1st, 2016 snapshot, I found this.

![](https://i.imgur.com/87WBakG.png)

>Answer: **IceCTF{Th3y'11_n3v4r_f1|\|d_m4h_fl3g_1n_th3_p45t}**

---

## chemtrails

350 points

![](https://i.imgur.com/yFt8rqT.png)

So we're given a boarding pass and tasked to find the seat number.

![](https://i.imgur.com/uyrClMw.png)

I cropped out the barcode on the boarding pass.

![](https://i.imgur.com/Gf8mPnN.png)

I then used an free online barcode reader from [Inline](https://online-barcode-reader.inliteresearch.com/).

![](https://i.imgur.com/q6rYDCL.png)

Selected the barcode type and read the results.

![](https://i.imgur.com/rAJ0BXI.png)

>Answer: **22B**

