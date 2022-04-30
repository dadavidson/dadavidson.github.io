---
layout: post
title: "OverTheWire: Bandit 11-15"
date: 2021-04-09 15:03:01
tags: [Security, Overthewire, Linux, Pentesting]
style: border
color: primary
description: OTW - Bandit Walkthrough 11-15
---

Welcome to another post on our series of [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)! This post covers my walkthrough of levels 11-15. If you would like to jump to specific level then each level with be accompanied with the username and password. I will be appending consecutive levels as I have to time to post. Enjoy!

## Bandit 11

[http://overthewire.org/wargames/bandit/bandit11.html](http://overthewire.org/wargames/bandit/bandit11.html)

The password for the next level is stored in the file **data-orginal.txt**, which contains base64 encoded data

Lucky for us there is a utility called base64. Let see what the built-in help tells us.

```console
bandit10@bandit:~$ base64 --help
Usage: base64 [OPTION]... [FILE]
Base64 encode or decode FILE, or standard input, to standard output.

With no FILE, or when FILE is -, read standard input.

Mandatory arguments to long options are mandatory for short options too.
  -d, --decode          decode data
  -i, --ignore-garbage  when decoding, ignore non-alphabet characters
  -w, --wrap=COLS       wrap encoded lines after COLS character (default 76).
                          Use 0 to disable line wrapping

      --help     display this help and exit
      --version  output version information and exit

The data are encoded as described for the base64 alphabet in RFC 4648.
When decoding, the input may contain newlines in addition to the bytes of
the formal base64 alphabet.  Use --ignore-garbage to attempt to recover
from any other non-alphabet bytes in the encoded stream.

GNU coreutils online help: <http://www.gnu.org/software/coreutils/>
Full documentation at: <http://www.gnu.org/software/coreutils/base64>
or available locally via: info '(coreutils) base64 invocation'
```

Seeing as the data-orginal.txt file is encoded the `-d` or `--decode` parameter looks like what we need.

```console
bandit10@bandit:~$ base64 --decode data-orginal.txt
The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
```

> **Username:** bandit11
> **Password:** IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR

---

## Bandit 12

[http://overthewire.org/wargames/bandit/bandit12.html](http://overthewire.org/wargames/bandit/bandit12.html)

The password for the next level is stored in the file **data-orginal.txt**, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

This means that it was encrypted with the ROT13 algorithm. In order to decrypt it, we have to replace every letter by the letter 13 positions ahead thus abc..xyz becomes:

**Input**	   ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz
**Output**	NOPQRSTUVWXYZABCDEFGHIJKLMnopqrstuvwxyzabcdefghijklm

 A useful tool to use would be tr. This tool can translate, squeeze, and/or delete characters from standard input.

```console
bandit11@bandit:~$ cat data-orginal.txt | tr A-Za-z N-ZA-Mn-za-m
The password is 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
```

**Helpful Reading Material**

- [ROT13 - Wikipedia](https://en.wikipedia.org/wiki/Rot13)

> **Username:** bandit12
> **Password:** 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu

---

## Bandit 13

[http://overthewire.org/wargames/bandit/bandit13.html](http://overthewire.org/wargames/bandit/bandit13.html)

First we need to make a temporary directory as suggested to unzip the files. Then copy `data-orginal.txt` and convert the ASCII text to binary (basically hexdump the content), for that we'll be using `xxd`.

```console
bandit12@bandit:~$ mkdir /tmp/me0w
bandit12@bandit:~$ cp data.txt /tmp/me0w/data-orginal.txt
bandit12@bandit:~$ cd /tmp/me0w
bandit12@bandit:/tmp/[me0w$ ls
data-orginal.txt
bandit12@bandit:/tmp/me0w$ xxd -r data-orginal.txt > data.bin
```

Next we want to figure out what the properties of the file are, for that we'll use `file`.

```console
bandit12@bandit:/tmp/me0w$ file data.bin
data.bin: gzip compressed data, was "data2.bin", from Unix, last modified: Fri Nov 14 10:32:20 2014, max compression
```

Awesome! Let's decompress it.

```console
bandit12@bandit:/tmp/me0w$ gzip -d data.bin
gzip: data.bin: unknown suffix -- ignored
```

Uh oh! `gzip` expects a proper extension, let's rename it.

```console
bandit12@bandit:/tmp/me0w$ mv data.bin data.gz
bandit12@bandit:/tmp/me0w$ gzip -d data.gz
bandit12@bandit:/tmp/me0w$ ls
data data-orginal.txt
```

Let's inspect it again with `file` like we did before.

```console
bandit12@bandit:/tmp/me0w$ file data
data: bzip2 compressed data, block size = 900k
```

Looks like this one is compressed via bzip. No worries we'll just do the same as before except we need to rename the file extension to `.bz2`.

```console
bandit12@bandit:/tmp/me0w$ mv data data.bz2
bandit12@bandit:/tmp/me0w$ bzip2 -d data.bz2
bandit12@bandit:/tmp/me0w$ ls
data data-orginal.txt
bandit12@bandit:/tmp/me0w$ file data
data: gzip compressed data, was "data4.bin", from Unix, last modified: Fri Nov 14 10:32:20 2014, max compression
```

Only a few more times...

```console
bandit12@bandit:/tmp/me0w$ mv data data.gz
bandit12@bandit:/tmp/me0w$ gzip -d data.gz
bandit12@bandit:/tmp/me0w$ ls
data data-orginal.txt
bandit12@bandit:/tmp/me0w$ file data
data: POSIX tar archive (GNU)
```

*Meanwhile...*
![](https://i.imgur.com/0xjkDHk.gif)

Did you watch it until the end? Okay sorry let's get back to it...

```console
bandit12@bandit:/tmp/me0w$ mv data data.gz
bandit12@bandit:/tmp/me0w$ gzip -d data.gz
bandit12@bandit:/tmp/me0w$ ls
data data-orginal.txt
bandit12@bandit:/tmp/me0w$ file data
data: POSIX tar archive (GNU)
bandit12@bandit:/tmp/me0w$ mv data data.tar
bandit12@bandit:/tmp/me0w$ tar -xvf data.tar
data5.bin
bandit12@bandit:/tmp/me0w$ file data5.bin
data5.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/me0w$ mv data5.bin data5.tar
bandit12@bandit:/tmp/me0w$ tar -xf data5.tar
bandit12@bandit:/tmp/me0w$ tar -xf data5.tar
bandit12@bandit:/tmp/me0w$ mv data5.bin data5.tarl^C
bandit12@bandit:/tmp/me0w$ ls
data-orginal.txt data5.tar data6.bin
bandit12@bandit:/tmp/me0w$ rm data5.tar
bandit12@bandit:/tmp/me0w$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/me0w$ mv data6.bin data6.bz2
bandit12@bandit:/tmp/me0w$ bzip2 -d data6.bz2
bandit12@bandit:/tmp/me0w$ ls
data-orginal.txt data6
bandit12@bandit:/tmp/me0w$ file data6
data6: POSIX tar archive (GNU)
bandit12@bandit:/tmp/me0w$ mv data6 data6.tar
bandit12@bandit:/tmp/me0w$ tar -xf data6.tar
bandit12@bandit:/tmp/me0w$ ls
data-orginal.txt data6.tar data8.bin
bandit12@bandit:/tmp/me0w$ rm data6.tar
```

Is this ever going to end?!
![](https://i.imgur.com/1QP7GLl.gif)

```console
bandit12@bandit:/tmp/me0w$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", from Unix, last modified: Fri Nov 14 10:32:20 2014, max compression
bandit12@bandit:/tmp/me0w$ mv data8.bin data8.gz
bandit12@bandit:/tmp/me0w$ gzip -d data8.gz
bandit12@bandit:/tmp/me0w$ ls
data-orginal.txt data8
bandit12@bandit:/tmp/me0w$ file data8
data8: ASCII text
bandit12@bandit:/tmp/me0w$ cat data8
The password is 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL
```

Got it!

> **Username:** bandit13
> **Password:** 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL

---

## Bandit 14

[http://overthewire.org/wargames/bandit/bandit14.html](http://overthewire.org/wargames/bandit/bandit14.html)

The password for the next level is stored in **/etc/bandit_pass/bandit14 and can only be read by user bandit14**. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. **Note: localhost** is a hostname that refers to the machine you are working on

This one is fairly simple. Just ssh into the next host using the private key provided.

```console
bandit13@bandit:~$ ls
sshkey.private
bandit13@bandit:~$ ssh bandit14@localhost -i sshkey.private
Could not create directory '/home/bandit13/.ssh'.
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is SHA256:98UL0ZWr85496EtCRkKlo20X3OPnyPSB5tB5RPbhczc.
Are you sure you want to continue connecting (yes/no)?
...
bandit14@bandit:~$
```

Here's a brief diagram explaining how SSH Private/Public keys work:
![](https://i.imgur.com/iS3gROA.png)

Now we just have to `cat` the password file for the current level like so.

```console
bandit14@bandit:~$ cat /etc/bandit_pass/bandit14
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
```

> **Username:** bandit14
> **Password:** 4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e  (Also uses sshkey.private file)

---

## Bandit 15

[http://overthewire.org/wargames/bandit/bandit15.html](http://overthewire.org/wargames/bandit/bandit15.html)

The password for the next level can be retrieved by submitting the password of the current level to **port 30000 on localhost**.

For the sake of being brief within this post I would suggest learning about a tool called **netcat** aka `ncat` - *The Network Swiss Army Knife*. Essentially **netcat** is a utility capable of establishing a TCP or UDP connection between two computers, meaning it can write and read through an open port. 

```console
bandit14@bandit:~$ cat /etc/bandit_pass/bandit14
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
bandit14@bandit:~$ cat /etc/bandit_pass/bandit14 | ncat localhost 30000
Correct!
BfMYroe26WYalil77FoDi9qh59eK5xNr
```

For all you Network+ guys think client-server model. The beauty of `ncat` is that it is capable being both the client and the server aka listener and sender. This is a great tool for sysadmins, network engineers, and hackers.

`nc <options> <host> <port>`

> **Username:** bandit15
> **Password:** BfMYroe26WYalil77FoDi9qh59eK5xNr

---