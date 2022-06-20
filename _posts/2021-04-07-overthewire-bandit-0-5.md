---
layout: post
title: "OverTheWire: Bandit 0-5"
date: 2021-04-04 23:18:06
tags: [Security, Overthewire, Linux, Pentesting]
style: border
color: primary
description: OTW - Bandit Walkthrough 0-5
---

I think [TryHackMe](https://tryhackme.com/) and [HackTheBox](https://www.hackthebox.eu/) are great resources for hacking and provide a nice platform for cyber security students to learn many things within a lab environment. That said, those resources also come with a price $$$ so I set out to find a free resource that every person interested in cyber security can try without paying a cent thanks to the good ole staff at [OverTheWire](https://overthewire.org/information/staff.html)! 

This first set of challenges or <span style="color:#9ADD15">*"wargames"*</span> is called [Bandit](https://overthewire.org/wargames/bandit/) and I believe it is good introduction to some cyber security challenges as well as it helps to hone your Linux skills. Here is my walkthrough of each level. If you would like to jump to specific level then each level with be accompanied with the username and password. I will be appending consecutive levels as I have to time to post. Enjoy!

## Bandit 0
[http://overthewire.org/wargames/bandit/bandit0.html](http://overthewire.org/wargames/bandit/bandit0.html)

In their website they give us the username and password for bandit0 and we have to find the password for bandit1

> **Username:** bandit0
> **Password:** bandit0 

---

## Bandit 1 
[http://overthewire.org/wargames/bandit/bandit1.html](http://overthewire.org/wargames/bandit/bandit1.html)

Nothing too difficult here, ssh into bandit0@bandit.labs.overthewire.org on port 2220. 
Type `ls` and it shows a readme file. Let's read what's inside it with cat command.

```console
bandit0@bandit:~$ ls
readme
bandit0@bandit:~$ cat readme
boJ9jbbUNNfktd78OOpsqOltutMc3MY1
```
That's our password. Easy!

> **Username:** bandit1
> **Password:** boJ9jbbUNNfktd78OOpsqOltutMc3MY1

---

## Bandit 2
[http://overthewire.org/wargames/bandit/bandit2.html](http://overthewire.org/wargames/bandit/bandit2.html)

This level states the next password is saved in a file named -
Type `ls` and it shows the file named -. Here we cannot simply type: `cat -`. It will fail.
In order to read files that start with a dash, you have to redirect them to stdin with the < operator.   By the way stdin: Stands for "standard input."

```console
bandit1@bandit:~$ ls -l
total 4
-rw-r----- 1 bandit2 bandit1 33 May  7  2020 -
bandit1@bandit:~$ cat <-
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
```

The goal of stdin is to work with input. This can also be redirected. For example, instead of typing the input from the keyboard, it can also be loaded from a file.

For example: In this command, cat will take its input directly from the hello.txt file.

```console
bandit1@bandit:~$ cat < hello.txt
Hello World!
```

Another solution is to use `./filename`.
```console
bandit1@bandit:~$ cat ./-
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
```

> **Username:** bandit2
> **Password:** CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9

**Helpful Reading Material**

- [https://unix.stackexchange.com/questions/189251/how-to-read-dash-files/189252](https://unix.stackexchange.com/questions/189251/how-to-read-dash-files/189252)

---

## Bandit 3
[http://overthewire.org/wargames/bandit/bandit3.html](http://overthewire.org/wargames/bandit/bandit3.html)

The password for the next level is stored in a file called *spaces in this filename.* 

If we were to `cat spaces in the filename` then the shell doesn't know how to handle the spaces so it would interpret this as:

```console
bandit2@bandit:~$ cat spaces in this filename
cat: spaces: No such file or directory
cat: in: No such file or directory
cat: this: No such file or directory
cat: filename: No such file or directory
```

As you can see it tries to treat each word as a separate file. In order to read files with spaces in the name you have to surround the file name in quotation marks like so:

```console
bandit2@bandit:~$ cat "spaces in this filename"
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
```
Another solution is to use an escape character such as `\` which reads the command as a continuation. You can think of this as removing or ignoring the special meaning of certain characters or words to the shell. In the example below we're using the backslash escape character to remove or ignore the spaces.

```console
bandit2@bandit:~$ cat spaces\ in\ this\ filename
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
```

> **Username:** bandit3
> **Password:** UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK

---

## Bandit 4
[http://overthewire.org/wargames/bandit/bandit4.html](http://overthewire.org/wargames/bandit/bandit4.html)

The password is stored in a hidden file in the **inhere** directory. I ran `ls` to see what files and directories there are and then I ran `cd` to move into the inhere folder. 

All hidden files and folders in Linux are stored with a dot in front of their name. If we take a look at the man page for `ls` which we can do by running `man ls` we see that the `-a` parameter specifies `ls` to list **all** files. 

```console
...
-a, --all
              do not ignore entries starting with .
...
```

So we can  run `ls -a` to see all of the files including the hidden ones.

```console
bandit3@bandit:~/inhere$ ls -a
.  ..  .hidden
```

The hidden file is named *.hidden* and after running `cat .hidden` we get the password like so:

```console
bandit3@bandit:~/inhere$ cat .hidden
pIwrPrtPN36QITSp3EQaw936yaFoFgAB
```

> **Username:** bandit4
> **Password:** pIwrPrtPN36QITSp3EQaw936yaFoFgAB

---

## Bandit 5
[http://overthewire.org/wargames/bandit/bandit5.html](http://overthewire.org/wargames/bandit/bandit5.html)

The password for the next level is stored in the only human-readable file in the **inhere** directory.

So we `cd` into the inhere directory and `ls -al` to give us a long listing `-l` and list all files `-a` parameters.

```console
bandit4@bandit:~/inhere$ ls -al
total 48
drwxr-xr-x 2 root    root    4096 May  7  2020 .
drwxr-xr-x 3 root    root    4096 May  7  2020 ..
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file00
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file01
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file02
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file03
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file04
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file05
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file06
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file07
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file08
-rw-r----- 1 bandit5 bandit4   33 May  7  2020 -file09
```

Okay so as you might imagine we could `cat` each file until we finally got the output we desired but programatically there's a better way. But first let me introduce you to the `file` command. This nifty little guy tells us what type of file we're dealing with if we pass it the file name. Let's test this:

```console
bandit4@bandit:~/inhere$ file ./-file00
./-file00: data
```

Okay so this file is considered a *data* file type. Now that we know how to determine file types we just need to run this command on each file name one by one right? True we could but again there's a better way. Let's pass a wildcard to the `file` command and see what happens this time. A wildcard matches any character zero or more times and is usually referenced by using the * (asterisk) character.  

```console
bandit4@bandit:~/inhere$ file ./-file0*
./-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: data
./-file06: data
./-file07: ASCII text
./-file08: data
./-file09: data
```

So using the wildcard on the last character of the filename began matching on every file in the directory. Now you can see an odd ball sticks out amongst the rest of the files. The filename `-file07` is the only file with type *ASCII text*. Let's `cat` that file and see what we get:

```console
bandit4@bandit:~/inhere$ cat ./-file07
koReBOKuIDDepwhWk7jZC0RTdopnAYKh
```

> **Username:** bandit5
> **Password:** koReBOKuIDDepwhWk7jZC0RTdopnAYKh

---
