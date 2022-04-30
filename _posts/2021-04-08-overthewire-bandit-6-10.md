---
layout: post
title: "OverTheWire: Bandit 6-10"
date: 2021-04-08 20:10:06
tags: [Security, Overthewire, Linux, Pentesting]
style: border
color: primary
description: OTW - Bandit Walkthrough 6-10
---

Welcome to another post on our series of [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)! This post covers my walkthrough of levels 6-10. If you would like to jump to specific level then each level with be accompanied with the username and password. I will be appending consecutive levels as I have to time to post. Enjoy!

## Bandit 6

[http://overthewire.org/wargames/bandit/bandit6.html](http://overthewire.org/wargames/bandit/bandit6.html)

The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:

​					*human-readable*
​					*1033 bytes in size*
​					*not executable*

To start lets `cd` into the inhere folder and run `ls` to see the files and folders.

```console
bandit5@bandit:~$ ls
inhere
bandit5@bandit:~$ cd inhere/
bandit5@bandit:~/inhere$ ls
maybehere00  maybehere03  maybehere06  maybehere09  maybehere12  maybehere15  maybehere18
maybehere01  maybehere04  maybehere07  maybehere10  maybehere13  maybehere16  maybehere19
maybehere02  maybehere05  maybehere08  maybehere11  maybehere14  maybehere17
bandit5@bandit:~/inhere$ cd maybehere00
bandit5@bandit:~/inhere/maybehere00$ ls -la
total 72
drwxr-x---  2 root bandit5 4096 May  7  2020 .
drwxr-x--- 22 root bandit5 4096 May  7  2020 ..
-rwxr-x---  1 root bandit5 1039 May  7  2020 -file1
-rwxr-x---  1 root bandit5  551 May  7  2020 .file1
-rw-r-----  1 root bandit5 9388 May  7  2020 -file2
-rw-r-----  1 root bandit5 7836 May  7  2020 .file2
-rwxr-x---  1 root bandit5 7378 May  7  2020 -file3
-rwxr-x---  1 root bandit5 4802 May  7  2020 .file3
-rwxr-x---  1 root bandit5 6118 May  7  2020 spaces file1
-rw-r-----  1 root bandit5 6850 May  7  2020 spaces file2
-rwxr-x---  1 root bandit5 1915 May  7  2020 spaces file3
```

Okay so what we have here is a bunch of folders nested inside other folder as well as a couple of files.
As stated we have to *find* a file that is *human-readable*, *1033 bytes in size*, and *not executable*.
We can use the `find` command for this. So once again we always *RTFM* aka *read the f****ing manual*. We need to look for ways of filtering out the properties of the file. I found `-size`, `-type`, and `-executable` options.

```console
-size n[cwbkMG]
              File uses n units of space, rounding up.  The following suffixes can be used:

              `b'    for 512-byte blocks (this is the default if no suffix is used)

              `c'    for bytes

              `w'    for two-byte words

              `k'    for Kilobytes (units of 1024 bytes)

              `M'    for Megabytes (units of 1048576 bytes)

              `G'    for Gigabytes (units of 1073741824 bytes)

              The size does not count indirect blocks, but it does count blocks in sparse files that
              are not actually allocated.  Bear in mind that the `%k' and `%b' format specifiers  of
              -printf  handle  sparse  files  differently.   The  `b' suffix always denotes 512-byte
              blocks and never 1 Kilobyte blocks, which is different to the behaviour of -ls.

              The + and - prefixes signify greater than and less than, as usual.  Bear in mind  that
              the  size  is  rounded  up  to the next unit. Therefore -size -1M is not equivalent to
              -size -1048576c.  The former only matches empty files, the latter matches files from 1
              to 1,048,575 bytes.

-type c
              File is of type c:

              b      block (buffered) special

              c      character (unbuffered) special

              d      directory

              p      named pipe (FIFO)

              f      regular file

              l      symbolic  link; this is never true if the -L option or the -follow option is in
                     effect, unless the symbolic link is broken.  If you want to search for symbolic
                     links when -L is in effect, use -xtype.

              s      socket

              D      door (Solaris)

              To  search  for  more  than one type at once, you can supply the combined list of type
              letters separated by a comma `,' (GNU extension).

-executable
              Matches files which are executable and directories which are  searchable  (in  a  file
              name  resolution  sense).  This takes into account access control lists and other per‐
              missions artefacts which the -perm test ignores.  This test makes use of the access(2)
              system call, and so can be fooled by NFS servers which do UID mapping (or root-squash‐
              ing), since many systems implement access(2) in the client's kernel and so cannot make
              use  of  the  UID  mapping information held on the server.  Because this test is based
              only on the result of the access(2) system call, there is no guarantee that a file for
              which this test succeeds can actually be executed.
```

If couple these parameters together this should give us what we're looking for.

```console
bandit5@bandit:~/inhere$ find -type f -size 1033c ! -executable
./maybehere07/.file2
bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
DXjZPULLxYr17uwoI01bNLQbtFemEgo7
```

Yep there's our password for the next level!

> **Username:** bandit6
> **Password:** DXjZPULLxYr17uwoI01bNLQbtFemEgo7

---

## Bandit 7

[http://overthewire.org/wargames/bandit/bandit7.html](http://overthewire.org/wargames/bandit/bandit7.html)

The password for the next level is stored somewhere on the server and has all of the following properties:

​					*owned by user bandit7*
​					*owned by group bandit6*
​					*33 bytes in size*

Just like the previous level I used the `find` command below from root folder `/` to find a file owned by user bandit7, owned by group bandit6 and 33 bytes of size.

```console
bandit6@bandit:~$ find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
/var/lib/dpkg/info/bandit7.password
```

**What does each parameter mean?**
- `-user` the owner of the file.
- `-group` the group owner of the file.
- `-size` the size of the file.
- `2>/dev/null` redirects error messages to null so that they do not show on stdout.

So we `cat` that file:

```console
bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
```

> **Username:** bandit7
> **Password:** HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs

---

## Bandit 8

[http://overthewire.org/wargames/bandit/bandit8.html](http://overthewire.org/wargames/bandit/bandit8.html)

The password for the next level is stored in the file **data.txt** next to the word **millionth**

This one is fairly easy. All we have to do `cat data.txt` and then just pipe it `|` to `grep`

```console
bandit7@bandit:~$ ls
data.txt
bandit7@bandit:~$ cat data.txt | grep millionth
millionth       cvX2JJa4CFALtqS87jk27qwqGhBM9plV
```
Think of `grep` as the search or find feature in any text editor you've used before except much more powerful!

> **Username:** bandit8
> **Password:** cvX2JJa4CFALtqS87jk27qwqGhBM9plV

---

## Bandit 9

[http://overthewire.org/wargames/bandit/bandit9.html](http://overthewire.org/wargames/bandit/bandit9.html)

The password for the next level is stored in the file **data.txt** and is the only line of text that occurs only once

More piping! Here you might need to reference the `man` pages on two tools `uniq` and `sort`

First let’s sort the strings, get the count of how many times they appear in data.txt and sort them again, so the string with appearance of 1 is on top. 

```console
bandit8@bandit:~$ cat data.txt | sort | uniq -c | sort
      1 UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
     10 07KC3ukwX7kswl8Le9ebb3H3sOoNTsR2
     10 0efnqHY1ZTNRu4LsDX4D73DsxIQq7RuJ
     10 0N65ZPpNGkUJePzFxctCRZRXVrCbUGfm
     10 0Xo6DLyK5izRqEtBA7sW2SRmlAixWYSg
```

By the way `-c` tells us how many time the values on adjacent lines have occurred

Another way is to pipe it one more time to grep with -v 10 which will invert the match inturn only revealing the string we want, but you already figured out how many times other strings appear by that point ;)

```console
bandit8@bandit:~$ cat data.txt | sort | uniq -c | sort | grep -v 10
      1 UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
```

A much cleaner and quicker solution would be to use the command below:

```console
sort data.txt | uniq -u
```

Also important to note is if you don’t sort the lines first, `uniq` has trouble matching unique values unless the lines are adjacent and doesn’t care if the string will show again later.

> **Username:** bandit9
> **Password:** UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR

---

## Bandit 10

[http://overthewire.org/wargames/bandit/bandit10.html](http://overthewire.org/wargames/bandit/bandit10.html)

The password for the next level is stored in the file **data.txt** in one of the few human-readable strings, preceded by several *‘=’* characters.

According to the hint, the file contains both strings and binary data which can make it difficult to read. So we use to the `strings` command to sort out plain text from the binary garble and once again pipe it to `grep` to output only the lines preceded by several equal signs `=`

```console
bandit9@bandit:~$ strings data.txt | grep ====
========== the*2i"4
========== password
Z)========== is
&========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
```

> **Username:** bandit10
> **Password:** truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk

---