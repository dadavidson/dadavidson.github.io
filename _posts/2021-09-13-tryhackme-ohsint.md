---
layout: post
title:  "TryHackMe | OhSINT"
date:   2021-09-13 22:30:00
tags: [Security, TryHackMe, OSINT]
style: border
color: primary
description: "TryHackMe OhSINT Writeup"
---

This is my write-up for the room [OhSINT](https://tryhackme.com/room/ohsint) on TryHackMe.

![](https://i.imgur.com/XFRCDYG.png)

**P.S: I highly encourage you to try solving the challenges on your own first then check this writeup if you are stuck.**

What is OSINT? 

>Open-source intelligence is a multi-factor methodology for collecting, analyzing and making decisions about data accessible in publicly available sources to be used in an intelligence context. In the intelligence community, the term "open" refers to overt, publicly available sources. -[Wikipedia](https://en.wikipedia.org/wiki/Open-source_intelligence)

## Step 1 - Download the task files

![](https://i.imgur.com/0Dm9u2y.png)

We get a familiar picture - the WindowsXP wallpaper.

![](https://i.imgur.com/OslQZvg.jpeg)

## Step 2 - Enumerating the photo

To see if we can deduce any information from the photo we need to anaylze the metadata. We can do this with a tool called `exiftool`.

```console
# exiftool WindowsXP.jpg
xifTool Version Number         : 12.30
File Name                       : WindowsXP.jpg
Directory                       : .
File Size                       : 229 KiB
File Modification Date/Time     : 2021:09:14 00:33:55-05:00
File Access Date/Time           : 2021:09:14 02:35:35-05:00
File Inode Change Date/Time     : 2021:09:14 02:35:35-05:00
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
XMP Toolkit                     : Image::ExifTool 11.27
GPS Latitude                    : 54 deg 17' 41.27" N
GPS Longitude                   : 2 deg 15' 1.33" W
Copyright                       : OWoodflint
Image Width                     : 1920
Image Height                    : 1080
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 1920x1080
Megapixels                      : 2.1
GPS Latitude Ref                : North
GPS Longitude Ref               : West
GPS Position                    : 54 deg 17' 41.27" N, 2 deg 15' 1.33" W
```

We have several pieces of information here but what stands out the most in the COPYRIGHT name, **OWoodflint**.

## Step 3 - Dorking the Googles

>Google is your friend

A simple google search of the name **OWoodflint** returns some pretty juicy information.

![](https://i.imgur.com/sezeQLa.png)

Let's go through the top 3 results.

- What is this users avatar of?

Found this through checking the Twitter account

![](https://i.imgur.com/70pmBte.png)

- What city is this person in?

The person made a tweet regarding "free wifi" and publicly shared a BSSID. Let's cross-reference the BSSID with [wigle.net](https://wigle.net/). Here's a great article on [wigle.net](https://wigle.net/) - [OSINT: Tracking the Suspect's Precise Location Using Wigle.net](https://www.hackers-arise.com/post/osint-tracking-the-suspect-s-precise-location-using-wigle-net).

![](https://i.imgur.com/NLi7r3f.png)

You have to create an account to access [Wigle.net](https://wigle.net/). Once that's done you just input the BSSID we gathered from Twitter and we get a pin on London.

![](https://i.imgur.com/rAj52r4.png)

- Whats the SSID of the WAP he connected to?

[Wigle.net](https://wigle.net/) also reveals to us the SSID/ESSID of the WAP (Wireless Access Point) he's connected to.

![](https://i.imgur.com/IQxEgaf.png)

- What is his personal email address?

Looking at the other links we gained in our intelligence search you'll find his personal email on Github.

![](https://i.imgur.com/qBKJIDB.png)

- What site did you find his email address on?

Github

- Where has he gone on holiday?

If we look at Mr. Oliver Wood Flints blog we find that he is currently in **New York**.

![](https://i.imgur.com/iIQgGTi.png)

- What is this persons password?

Last but not least this is probably the most challenging on the flags. My first instinct was to check the source code of the blog for any information. To my surprise I found a peculiar string laying in the open.

![](https://i.imgur.com/zfAgQ1q.png)

You can also mouse over and highlight the blog post to reveal the password.

![](https://i.imgur.com/TjPiWvs.png)

Overall this was a very fun challenge. I look forward to diving more into OSINT later on. I think it definetly brings out the detective in me and I revel in the hunt.