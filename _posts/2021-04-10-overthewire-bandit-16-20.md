---
layout: post
title: "OverTheWire: Bandit 16-20"
date: 2021-04-10 18:35:00
tags: [Security, Overthewire, Linux, Pentesting]
style: border
color: primary
description: OTW - Bandit Walkthrough 16-20
---

Welcome to another post on our series of [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)! This post covers my walkthrough of levels 16-20. If you would like to jump to specific level then each level with be accompanied with the username and password. I will be appending consecutive levels as I have to time to post. Enjoy!

## Bandit 16

[http://overthewire.org/wargames/bandit/bandit16.html](http://overthewire.org/wargames/bandit/bandit16.html)

The password for the next level can be retrieved by submitting the password of the current level to **port 30001 on localhost** using SSL encryption.

**Helpful note: Getting “HEARTBEATING” and “Read R BLOCK”? Use -ign_eof and read the “CONNECTED COMMANDS” section in the manpage. Next to ‘R’ and ‘Q’, the ‘B’ command also works in this version of that command…**

As stated before we just have to create an SSL encrypted connection to the localhost on 30001 then submit the current password for this level. Here are two ways of doing this:

**Method 1: ncat**
```console
bandit15@bandit:~$ ncat --ssl localhost 30001
BfMYroe26WYalil77FoDi9qh59eK5xNr
Correct!
cluFn7wTiGryunymYOu4RcffSxQluehd
```

**Method 2: openssl s_client**
```console
bandit15@bandit:~$ openssl s_client -connect localhost:30001 -ign_eof
CONNECTED(00000003)
depth=0 CN = localhost
verify error:num=18:self signed certificate
verify return:1
depth=0 CN = localhost
verify return:1
---
Certificate chain
 0 s:/CN=localhost
   i:/CN=localhost
---
Server certificate
-----BEGIN CERTIFICATE-----
MIICBjCCAW+gAwIBAgIEfftLGTANBgkqhkiG9w0BAQUFADAUMRIwEAYDVQQDDAls
b2NhbGhvc3QwHhcNMjEwNDEzMDgzODA3WhcNMjIwNDEzMDgzODA3WjAUMRIwEAYD
VQQDDAlsb2NhbGhvc3QwgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAMLfXBVa
jVKDHlA3U+S0hBMJMJlfue3xKECpmx1Ajp4/khUuWwvPB7+wLjqasBO2WfFYJzcq
z9t7FfAPIlYjgvOTQs5X4vQ1aGzanvnNn+1VknpOnFAJQBSFq6ZD3ipWrhwm9XZq
8CgFhTGp9IPthZp8Y0B7OgobhlMtXD/zLaTbAgMBAAGjZTBjMBQGA1UdEQQNMAuC
CWxvY2FsaG9zdDBLBglghkgBhvhCAQ0EPhY8QXV0b21hdGljYWxseSBnZW5lcmF0
ZWQgYnkgTmNhdC4gU2VlIGh0dHBzOi8vbm1hcC5vcmcvbmNhdC8uMA0GCSqGSIb3
DQEBBQUAA4GBAMFH9rsZovwnb5k71/MpyCnXEwGlIhixUu6qfi1kiFvhJ6lJCvaO
weOYxV4oJy1OEB0LSEAQOnSPfzC8dDasijFcdVhuIGGPuQ2GZ05nCiiIZUNnrMRB
0z2RuRxgxMVjOvcSIJyvwyjVH4jY4I434fMyldePLxO1POLd1cxoKNTO
-----END CERTIFICATE-----
subject=/CN=localhost
issuer=/CN=localhost
---
No client certificate CA names sent
Peer signing digest: SHA512
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 1019 bytes and written 269 bytes
Verification error: self signed certificate
---
New, TLSv1.2, Cipher is ECDHE-RSA-AES256-GCM-SHA384
Server public key is 1024 bit
Secure Renegotiation IS supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
SSL-Session:
    Protocol  : TLSv1.2
    Cipher    : ECDHE-RSA-AES256-GCM-SHA384
    Session-ID: 719825D6A1805E05ACF8C98191CCDB80B1E8C3B3DD99828A717A7AD97355B40C
    Session-ID-ctx:
    Master-Key: 8A1D83103F65ACAD73B25B7A6EBB5664461ED873B58D40B2D0886D3CA937036CBD53A7DB064AB1AE2B91B6824C9F48F3
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - f2 5b 83 7e f0 ca 58 ca-aa f3 8f 83 b9 65 d5 23   .[.~..X......e.#
    0010 - 70 f3 79 64 34 79 8f d0-86 06 01 01 8d 33 c1 95   p.yd4y.......3..
    0020 - 12 7d cb 91 cc 98 4b da-6c a4 bf c5 73 4a a3 11   .}....K.l...sJ..
    0030 - 36 83 37 be 06 c6 e5 18-4d d7 37 2f 2f 9a f1 b7   6.7.....M.7//...
    0040 - 3b 12 0f ba e3 2d ef 67-67 b1 4b c1 18 0f 6e 36   ;....-.gg.K...n6
    0050 - b0 9a c0 11 f0 e4 e8 32-6a 95 87 6a 94 f3 91 d0   .......2j..j....
    0060 - b8 e1 1e ac c6 3c c6 0b-c0 ee 36 6f 31 63 94 93   .....<....6o1c..
    0070 - 26 89 42 0d 98 89 c2 4e-7e 81 ee ca 3b e5 3d 4b   &.B....N~...;.=K
    0080 - bd 7a 24 6a ed 78 89 26-4e 89 8f 0a 36 b3 95 02   .z$j.x.&N...6...
    0090 - 32 49 1a 45 4b 1f 09 72-b4 47 15 eb ed a3 ee c1   2I.EK..r.G......

    Start Time: 1618615502
    Timeout   : 7200 (sec)
    Verify return code: 18 (self signed certificate)
    Extended master secret: yes
---
BfMYroe26WYalil77FoDi9qh59eK5xNr
Correct!
cluFn7wTiGryunymYOu4RcffSxQluehd

closed
```

> **Username:** bandit16
> **Password:** cluFn7wTiGryunymYOu4RcffSxQluehd

---

## Bandit 17

[http://overthewire.org/wargames/bandit/bandit17.html](http://overthewire.org/wargames/bandit/bandit17.html)

The credentials for the next level can be retrieved by submitting the password of the current level to **a port on localhost in the range 31000 to 32000**. First find out which of these ports have a server listening on them. Then find out which of those speak SSL and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

If you remember the last level they gave us the port but this time they want us to find the port in the range of 31000-32000. Here is a simple way we can check to see if a port/service is open on a system using one of my favorite enumeration tools. Let me introduce you to `nmap`.

```console
bandit16@bandit:~$ nmap localhost -p 31000-32000

Starting Nmap 7.40 ( https://nmap.org ) at 2021-04-17 01:55 CEST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00022s latency).
Not shown: 996 closed ports
PORT      STATE SERVICE
31046/tcp open  unknown
31518/tcp open  unknown
31691/tcp open  unknown
31790/tcp open  unknown
31960/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 0.08 seconds
```

Ah well...good news is we limited our possible suspect ports to: 
- **31046**
- **31518**
- **31691**
- **31790**
- **31960**

Bad news is that we still don't know which one of these ports is the correct one that speaks SSL. Let's see if we can't try using the `-sV` parameter to do better service detection for us.

```console
bandit16@bandit:~$ nmap -sV localhost -p 31000-32000

Starting Nmap 7.40 ( https://nmap.org ) at 2021-04-17 01:50 CEST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00044s latency).
Not shown: 996 closed ports
PORT      STATE SERVICE     VERSION
31046/tcp open  echo
31518/tcp open  ssl/echo
31691/tcp open  echo
31790/tcp open  ssl/unknown
31960/tcp open  echo
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port31790-TCP:V=7.40%T=SSL%I=7%D=4/17%Time=607A22E0%P=x86_64-pc-linux-g
SF:nu%r(GenericLines,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20cu
SF:rrent\x20password\n")%r(GetRequest,31,"Wrong!\x20Please\x20enter\x20the
SF:\x20correct\x20current\x20password\n")%r(HTTPOptions,31,"Wrong!\x20Plea
SF:se\x20enter\x20the\x20correct\x20current\x20password\n")%r(RTSPRequest,
SF:31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20password\
SF:n")%r(Help,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x
SF:20password\n")%r(SSLSessionReq,31,"Wrong!\x20Please\x20enter\x20the\x20
SF:correct\x20current\x20password\n")%r(TLSSessionReq,31,"Wrong!\x20Please
SF:\x20enter\x20the\x20correct\x20current\x20password\n")%r(Kerberos,31,"W
SF:rong!\x20Please\x20enter\x20the\x20correct\x20current\x20password\n")%r
SF:(FourOhFourRequest,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20c
SF:urrent\x20password\n")%r(LPDString,31,"Wrong!\x20Please\x20enter\x20the
SF:\x20correct\x20current\x20password\n")%r(LDAPSearchReq,31,"Wrong!\x20Pl
SF:ease\x20enter\x20the\x20correct\x20current\x20password\n")%r(SIPOptions
SF:,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20password
SF:\n");

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 88.08 seconds
```

Aha! This is a lot better because we've limited the list of suspect ports further to:
- **31518** which is running SSL/echo
- **31790** which is running SSL/unknown

That said we still have 50/50 chance between these two ports or do we...

![](https://media0.giphy.com/media/VhRK9ZABipMLRy7JM6/giphy.gif)

Notice below the scan...nmap gives a little more tid bit of information by showing what looks like a returned string value of "Wrong! Please enter the correct current password".

```console
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port31790-TCP:V=7.40%T=SSL%I=7%D=4/17%Time=607A22E0%P=x86_64-pc-linux-g
SF:nu%r(GenericLines,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20cu
SF:rrent\x20password\n")
```

Alright so through the science of deduction we can conclude that port 31790 is looking for a password. Let's try it and see what happens:

```console
bandit16@bandit:~$ ncat --ssl localhost 31790
cluFn7wTiGryunymYOu4RcffSxQluehd
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
```

Yep we were correct! We got a private key that can be used for authentication to the next level.

*Note: Just to show you that the other SSL port 31518...simply repeats back whatever we input.*

```console
bandit16@bandit:~$ ncat --ssl localhost 31518
cluFn7wTiGryunymYOu4RcffSxQluehd
cluFn7wTiGryunymYOu4RcffSxQluehd
```

We're not out of this level yet though. So now all we have to do is create a folder so we can store this ssh key somewhere.

```console
bandit16@bandit:~$ mkdir -p /tmp/me_bandit16-17
bandit16@bandit:~$ nano /tmp/me_bandit16-17/sshkey.private
<paste key here>
....
```
We also have to change the file permissions of the key file: `chmod 600 sshkey.private`

Now we pipe the key to ssh to login to `bandit17@localhost`

```console
bandit16@bandit:~$ ssh -i sshkey.private bandit17@localhost
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is SHA256:98UL0ZWr85496EtCRkKlo20X3OPnyPSB5tB5RPbhczc.
Are you sure you want to continue connecting (yes/no)? yes
bandit17@bandit:~$
```

Lastly remember in level 14 all passwords are stored in the `/etc/bandit_pass/` folder. So we just `cat` the file for the current level to get the password like so.

```console
bandit17@bandit:~$ cat /etc/bandit_pass/bandit17
xLYVMN9WE5zQ5vHacb0sZEVqbrp7nBTn
```

> **Username:** bandit17
> **Password:** xLYVMN9WE5zQ5vHacb0sZEVqbrp7nBTn

---

## Bandit 18

[http://overthewire.org/wargames/bandit/bandit18.html](http://overthewire.org/wargames/bandit/bandit18.html)

There are 2 files in the homedirectory: **passwords.old** and **passwords.new**. The password for the next level is in **passwords.new** and is the only line that has been changed between **passwords.old** and **passwords.new**

**NOTE: if you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, bandit19**

Pretty straight forward, we just have to find the differences between the two files. To do this perhaps `diff` seems like a sensible option.

```console
bandit17@bandit:~$ diff passwords.old passwords.new
42c42
< w0Yfolrc5bwjS4qw5mq1nnQi6mF03bii
---
> kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
```

Done! The password is the one on the right `kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd`.

I always advocate for different ways of doing things because you never know the `diff` tool may not be installed or available. So let's try doing it with `grep`.

```console
bandit17@bandit:~$ grep -xvFf passwords.old passwords.new
kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
```

Nice! Same result as before. Consider the parameters used in this command a little homework.
`man grep`

> **Username:** bandit18
> **Password:** kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd

---

## Bandit 19

[http://overthewire.org/wargames/bandit/bandit19.html](http://overthewire.org/wargames/bandit/bandit19.html)

The password for the next level is stored in a file **readme** in the homedirectory. Unfortunately, someone has modified **.bashrc** to log you out when you log in with SSH.

Hmm, once you ssh into the server you just log out. We need to do 2 things:

- Figure out why we log out once connected.
- Find a way to stay connected.

Let's start by our first objective. You might've noticed that some hidden files exist in the home directory, like `.bashrc` or `.bash_profile`. We need to find a way to read their content. SSH is the only command we used, so let's check the help pages, maybe there's a way to run a command upon executing?

`ssh --help` didn't reveal much info, let's try the man pages. `-t` looks promising.
We can use `t` to excute arbitray commands on the system.

Let's first use `ls -al` to see if the file is in the users home directory.
```console
bandit17@bandit:~$ ssh bandit18@localhost -t "ls -al"
This is the OverTheWire game server. More information on http://www.overthewire.org/wargames
Please note that wargame usernames are no longer level<X>, but wargamename<X>
e.g. vortex4, semtex2, ...
Note: at this moment, blacksun is not available.
bandit18@bandit.labs.overthewire.org's password:
total 24
drwxr-xr-x  2 root   root   4096 Nov 14 2014 .
drwxr-xr-x 172 root   root   4096 Jul 10 14:12 ..
-rw-r--r--  1 root   root   220 Apr 9 2014 .bash_logout
-rw-r-----  1 bandit19 bandit18 3660 Nov 14 2014 .bashrc
-rw-r--r--  1 root   root   675 Apr 9 2014 .profile
-rw-r-----  1 bandit19 bandit18  33 Nov 14 2014 readme
Connection to bandit.labs.overthewire.org closed.
```

Perfect! Looks like it is. So now lets `cat` the `readme` file to stdout.

```console
bandit17@bandit:~$ ssh bandit18@localhost -t "cat readme"
This is the OverTheWire game server. More information on http://www.overthewire.org/wargames
Please note that wargame usernames are no longer level<X>, but wargamename<X>
e.g. vortex4, semtex2, ...
Note: at this moment, blacksun is not available.
bandit18@bandit.labs.overthewire.org's password:
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
Connection to bandit.labs.overthewire.org closed.
```

Another possible solution is to just call a shell directly via `sh`.

```console
bandit17@bandit:~$ ssh bandit18@localhost -t "sh"
This is the OverTheWire game server. More information on http://www.overthewire.org/wargames
Please note that wargame usernames are no longer level<X>, but wargamename<X>
e.g. vortex4, semtex2, ...
Note: at this moment, blacksun is not available.
bandit18@bandit.labs.overthewire.org's password:
$ ls
readme
$ cat readme
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
```

So basically, we have two solutions:

- `ssh bandit18@localhost -t "cat readme" `

Or directly calling `sh` and have the regular interactive shell.

- `ssh bandit18@localhost -t "sh"`

> **Username:** bandit19
> **Password:** IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x

---

## Bandit 20

[http://overthewire.org/wargames/bandit/bandit20.html](http://overthewire.org/wargames/bandit/bandit20.html)

To gain access to the next level, you should use the `setuid` binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place **(/etc/bandit_pass)**, after you have used the setuid binary.

In this challenge, we'll get introduced to a new concept, which is [setuid](https://en.wikipedia.org/wiki/Setuid). Any file/directory in Linux has read, write, execute (if not directory) permissions for owner, group or other. You can read more about Linux file permissions [here](https://www.tutorialspoint.com/unix/unix-file-permission.htm).

**So why is this a big deal?**

Well a misconfigured setuid program could be disastrous as it could give allow privilege escalation from limited account to root access. [Root-me.org](https://www.root-me.org/en/Challenges/App-Script) has a few great examples on how to exploit it. Another interesting read is [nmap's interactive mode](https://gist.github.com/dergachev/7916152).

Alright, let's see what's the setuid program does.

```console
bandit19@bandit:~$ ls -l
total 8
-rwsr-x--- 1 bandit20 bandit19 7296 May  7  2020 bandit20-do
bandit19@bandit:~$ ./bandit20-do
Run a command as another user.
  Example: ./bandit20-do id
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
```

So in short this was a misconfigured `setuid` binary which allowed us to elevate and execute commands as the user bandit20. In turn, the file `/etc/bandit_pass/bandit20`where the password is stored  is owned by user bandit20 so we have full rights to read it via `cat`.

> **Username:** bandit20
> **Password:** GbKksEFF4yrVs6il55v6gwY5aVje5f0j

---