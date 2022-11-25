---
layout: post
title: "OverTheWire Leviathan 0-7"
date: 2022-06-19 14:00:00
style: border
color: primary
tags: [Security, Overthewire, Reverse-Engineering]
description: OTW - Leviathan Walkthrough 0-7
feature-img: https://i.imgur.com/ZPSZU1A.jpg
---

These challenges are called [Wargames](https://overthewire.org/wargames/). Wargames are similar to a CTF but are always ongoing. Typically, they are organized into levels that get progressively harder as you solve more of them. Wargames are an excellent way to practice for CTFs!

This set of challenges or *Wargames* is called [Leviathan](https://overthewire.org/wargames/leviathan/). Leviathan teaches the basics of Reverse Engineering and *nix commands. 

It states that *"This wargame doesn't require any knowledge about programming - just a bit of common sense and some knowledge about basic *nix commands."*

In my opinion though it requires a tad bit more than what they are stating. For anyone having a hard time going through these levels I'd recommend going through the follwing resources below:

- [C Programming Crash Course](https://youtu.be/yLNWu6AxftI)
- [Spying on Running Programs (strace, ltrace, system calls vs function calls)](https://youtu.be/2AmP7Pse4U0)

Here is my walkthrough of each level. Enjoy!

## Leviathan 0
[https://overthewire.org/wargames/leviathan/](https://overthewire.org/wargames/leviathan/)

 For the first level we must log into the leviathan lab instance, so we will SSH to leviathan0.

```console
kali@kali:~$ ssh leviathan0@leviathan.labs.overthewire.org -p 2223
```

<details>
  <summary>Show Answer</summary>

  Username: leviathan0

  Password: leviathan0
</details>

---

## Leviathan 0 -> 1
[https://overthewire.org/wargames/leviathan/leviathan1.html](https://overthewire.org/wargames/leviathan/leviathan1.html)

References:
- [https://www.computerhope.com/unix/ugrep.htm](https://www.computerhope.com/unix/ugrep.htm)

Let's see what files/directories are available.

```console
leviathan0@leviathan:~$ ls -al
total 28
drwxr-xr-x  4 leviathan0 leviathan0 4096 Jun 24 07:32 .
drwxr-xr-x 11 root       root       4096 Jun 24 07:32 ..
drwxr-x---  2 leviathan1 leviathan0 4096 Jun 15 11:38 .backup
-rw-r--r--  1 leviathan0 leviathan0  220 Apr  9  2014 .bash_logout
-rw-r--r--  1 leviathan0 leviathan0 3637 Apr  9  2014 .bashrc
drwx------  2 leviathan0 leviathan0 4096 Jun 24 07:32 .cache
-rw-r--r--  1 leviathan0 leviathan0  675 Apr  9  2014 .profile
```

`.backup` looks interesting, so let’s look in there and see if we can’t find anything.

```console
leviathan0@leviathan:~$ cd .backup/
leviathan0@leviathan:~/.backup$ ls -al
total 140
drwxr-x--- 2 leviathan1 leviathan0   4096 Jun 15 11:38 .
drwxr-xr-x 4 leviathan0 leviathan0   4096 Jun 24 07:32 ..
-rw-r----- 1 leviathan1 leviathan0 133259 Jun 15 11:38 bookmarks.html
```

Okay so there’s a big file with lots of bookmarks. Let's see if `grep` can sift through all the noise.

```console
leviathan0@leviathan:~/.backup$ wc bookmarks.html 
  1399   6601 133259 bookmarks.html
leviathan0@leviathan:~/.backup$ grep password bookmarks.html
<DT><A HREF="http://leviathan.labs.overthewire.org/passwordus.html | This will be fixed later, the password for leviathan1 is XXXXXXXXXX" ADD_DATE="1155384634" LAST_CHARSET="ISO-8859-1" ID="rdf:#$2wIU71">password to leviathan1</A>
```

<details>
  <summary>Show Answer</summary>

  Username: leviathan1

  Password: PPIfmI1qsA
</details>

---

## Leviathan 1 -> 2
[https://overthewire.org/wargames/leviathan/leviathan2.html](https://overthewire.org/wargames/leviathan/leviathan2.html)

References:
- [https://www.computerhope.com/jargon/s/setuid.htm](https://www.computerhope.com/jargon/s/setuid.htm)
- [https://www.thegeekstuff.com/2012/03/reverse-engineering-tools/](https://www.thegeekstuff.com/2012/03/reverse-engineering-tools/)

```console
leviathan1@leviathan:~$ ls -al
total 32
drwxr-xr-x  3 leviathan1 leviathan1 4096 Jun 24 07:40 .
drwxr-xr-x 11 root       root       4096 Jun 24 07:40 ..
-rw-r--r--  1 leviathan1 leviathan1  220 Apr  9  2014 .bash_logout
-rw-r--r--  1 leviathan1 leviathan1 3637 Apr  9  2014 .bashrc
drwx------  2 leviathan1 leviathan1 4096 Jun 24 07:40 .cache
-rw-r--r--  1 leviathan1 leviathan1  675 Apr  9  2014 .profile
-r-sr-x---  1 leviathan2 leviathan1 7501 Jun 15 11:38 check
```

We can see that the home directory stores an executable called `check`, so let’s run that and see what happens.

```console
leviathan1@leviathan:~$ ./check
password: 1234
Wrong password, Good Bye ...
```

Okay so we have a a binary called `check` with SetUID bit and it's asking for a password. Let’s try running it through `ltrace` and see what library calls are being made.

```console
leviathan1@leviathan:~$ ltrace ./check
__libc_start_main(0x804852d, 1, 0xffffd794, 0x80485f0 <unfinished ...>
printf("password: ")                                        = 10
getchar(0x8048680, 47, 0x804a000, 0x8048642password: a7a
)                = 97
getchar(0x8048680, 47, 0x804a000, 0x8048642)                = 55
getchar(0x8048680, 47, 0x804a000, 0x8048642)                = 97
strcmp("a7a", "sex")                                        = -1
puts("Wrong password, Good Bye ..."Wrong password, Good Bye ...
)                        = 29
+++ exited (status 0) +++
```

Looking at the output, we can see that the program is using the C library function `strcmp`, which compares two strings against one another. If they match then the condition becomes **TRUE**.
In this executable, it’s comparing the password input against the word “sex”. Let’s try inputing the string “sex” and see if it will work.

```console
leviathan1@leviathan:~$ ./check
password: sex
$ id
uid=12001(leviathan1) gid=12001(leviathan1) euid=12002(leviathan2) groups=12002(leviathan2),12001(leviathan1)
$ cat /etc/leviathan*/leviathan2
password_to_leviathan2
```

That worked so lastly we obtained a shell and cat the text file that contains the password for the next level.

<details>
  <summary>Show Answer</summary>

  Username: leviathan2

  Password: mEh5PNl10e
</details>

---

## Leviathan 2 -> 3
[https://overthewire.org/wargames/leviathan/leviathan3.html](https://overthewire.org/wargames/leviathan/leviathan3.html)

References:
- [https://www.computerhope.com/unix/uln.htm](https://www.computerhope.com/unix/uln.htm)
- [https://www.geeksforgeeks.org/code-injection-mitigation-example/](https://www.geeksforgeeks.org/code-injection-mitigation-example/)

Like the last challenge we will examine the binary using `ltrace`. Look at the output below:

```console
leviathan2@leviathan:~$ ltrace ./printfile "/etc/leviathan_pass/leviathan2"
__libc_start_main(0x804852d, 2, 0xffffd764, 0x8048600 <unfinished ...>
access("/etc/leviathan_pass/leviathan2", 4)                 = 0
snprintf("/bin/cat /etc/leviathan_pass/lev"..., 511, "/bin/cat %s", "/etc/leviathan_pass/leviathan2") = 39
system("/bin/cat /etc/leviathan_pass/lev"...XXXXXX
 <no return ...>
--- SIGCHLD (Child exited) ---
<... system resumed> )                                      = 0
+++ exited (status 0) +++
```

Let's break this down:

1. The executable binary takes a file name as argv input.
2. Calls the `access()` function to check if it has permissions to read that file (`O_RDONLY`).
3. Creates a string using the `sprintf` function, using this format `sprintf("/bin/cat %s", filename)`
4. Makes a `system()` call and executes the string.

Let's examine the `cat` behaviour:

```console
leviathan2@leviathan:/tmp/pyr0$ echo 1 > a
leviathan2@leviathan:/tmp/pyr0$ echo 2 > b
leviathan2@leviathan:/tmp/pyr0$ echo 3 > "a b"
leviathan2@leviathan:/tmp/pyr0$ cat a b
1
2
leviathan2@leviathan:/tmp/pyr0$ cat "/tmp/pyr0/a b"
3
leviathan2@leviathan:/tmp/pyr0$ cat /tmp/pyr0/a b 
1
2
leviathan2@leviathan:/tmp/pyr0$ ~/printfile /tmp/pyr0/a b
1
leviathan2@leviathan:/tmp/pyr0$ ~/printfile "/tmp/pyr0/a b"
1
2
```

Notice how `cat` handled it vs the `printfile` executable. From the output, we can see that there is a small security hole in the way this `printfile` program functions.
When `system("/bin/cat /tmp/pyr0/a b")` is executed, it prints the content of a and b, not the *"a b"* file itself. 

So how do we access the `/etc/leviathan_pass/leviathan3` file containing the password? 

Easy we can create a symbolic link using the `ln` command to that file.

Here's how:

1. Symlink `b` to `/etc/leviathan_pass/leviathan3`
2. Execute `printfile "/tmp/abatchy/printfile a b"`

```console
leviathan2@leviathan:/tmp/pyr0$ ln -s /etc/leviathan_pass/leviathan3 b
leviathan2@leviathan:/tmp/pyr0$ ~/printfile "/tmp/pyr0/a b"
1
password_to_leviathan3
```

<details>
  <summary>Show Answer</summary>

  Username: leviathan3

  Password: Q0G8j4sakn
</details>

---

## Leviathan 3 -> 4
[https://overthewire.org/wargames/leviathan/leviathan4.html](https://overthewire.org/wargames/leviathan/leviathan4.html)

Use what you learned from the previous levels to your advantage. Let’s begin by seeing what we have in our home directory.

```console
leviathan3@leviathan:~$ ls -la
total 32
drwxr-xr-x   2 root       root       4096 Mar 21  2015 .
drwxr-xr-x 172 root       root       4096 Jul 10 14:12 ..
-rw-r--r--   1 root       root        220 Apr  9  2014 .bash_logout
-rw-r--r--   1 root       root       3637 Apr  9  2014 .bashrc
-rw-r--r--   1 root       root        675 Apr  9  2014 .profile
-r-sr-x---   1 leviathan4 leviathan3 9962 Mar 21  2015 level3
```

We have another executable called `level3`, let’s run it and see what happens.

```console
leviathan3@leviathan:~$ ./level3
Enter the password> 1234
bzzzzzzzzap. WRONG
```

Run `ltrace` to see what the library calls are for this program.

```console
leviathan3@leviathan:~$ ltrace ./level3
__libc_start_main(0x80485fe, 1, 0xffffd794, 0x80486d0 <unfinished ...>
strcmp("h0no33", "kakaka")                       = -1
printf("Enter the password> ")                   = 20
fgets(Enter the password> 1234
"1234\n", 256, 0xf7fc9c20)                 = 0xffffd58c
strcmp("1234\n", "snlprintf\n")                  = -1
puts("bzzzzzzzzap. WRONG"bzzzzzzzzap. WRONG
)                       = 19
+++ exited (status 0) +++
```

Looking at the `ltrace` we can see that the `strcmp` function is being called twice. One of these calls is fake and trying to throw us off the scent.
Let's focus on the one that actually contains our input. It tries to compare the input against “snlprintf”; let’s try that as the password.

```console
leviathan3@leviathan:~$ ./level3
Enter the password> snlprintf
[You've got shell]!
$ whoami
leviathan4
$ cat /etc/leviathan_pass/leviathan4
password_to_leviathan4
```

<details>
  <summary>Show Answer</summary>

  Username: leviathan4
  
  Password: AgvropI4OA
</details>

---

## Leviathan 4 -> 5
[https://overthewire.org/wargames/leviathan/leviathan5.html](https://overthewire.org/wargames/leviathan/leviathan5.html)

References:
- [https://www.computerhope.com/jargon/b/binary.htm](https://www.computerhope.com/jargon/b/binary.htm)

```console
leviathan4@leviathan:~$ ls -la
total 24
drwxr-xr-x   3 root root       4096 Nov 14  2014 .
drwxr-xr-x 172 root root       4096 Jul 10 14:12 ..
-rw-r--r--   1 root root        220 Apr  9  2014 .bash_logout
-rw-r--r--   1 root root       3637 Apr  9  2014 .bashrc
-rw-r--r--   1 root root        675 Apr  9  2014 .profile
dr-xr-x---   2 root leviathan4 4096 Nov 14  2014 .trash
```

Let's look at the `.trash` directory.

```console
leviathan4@leviathan:~$ cd .trash
leviathan4@leviathan:~/.trash$ ls -la
total 16
dr-xr-x--- 2 root       leviathan4 4096 Nov 14  2014 .
drwxr-xr-x 3 root       root       4096 Nov 14  2014 ..
-r-sr-x--- 1 leviathan5 leviathan4 7425 Nov 14  2014 bin
leviathan4@leviathan:~/.trash$ ./bin
01010100 01101001 01110100 01101000 00110100 01100011 01101111 01101011 01100101 01101001 00001010
```

Okay so it outputs something in Binary. Search for a [Binary to ASCII converter](https://www.rapidtables.com/convert/number/binary-to-ascii.html) to do the conversion.

<details>
  <summary>Show Answer</summary>

  Username: leviathan5

  Password: EKKlTF1Xqs
</details>

---

## Leviathan 5 -> 6
[https://overthewire.org/wargames/leviathan/leviathan6.html](https://overthewire.org/wargames/leviathan/leviathan6.html)

Check the home directory.

```console
leviathan5@leviathan:~$ ls -la
total 28
drwxr-xr-x   2 root       root       4096 Nov 14  2014 .
drwxr-xr-x 172 root       root       4096 Jul 10 14:12 ..
-rw-r--r--   1 root       root        220 Apr  9  2014 .bash_logout
-rw-r--r--   1 root       root       3637 Apr  9  2014 .bashrc
-rw-r--r--   1 root       root        675 Apr  9  2014 .profile
-r-sr-x---   1 leviathan6 leviathan5 7634 Nov 14  2014 leviathan5
```

Another executable, okay. Let’s run it and see what we get.

```console
leviathan5@leviathan:~$ ./leviathan5
Cannot find /tmp/file.log
```

The executable is trying to pull information from a file that doesn’t exist. Run an `ltrace` and see what is being called.

```console
leviathan5@leviathan:~$ ltrace ./leviathan5
__libc_start_main(0x80485ed, 1, 0xffffd794, 0x8048690 <unfinished ...>
fopen("/tmp/file.log", "r")                      = 0
puts("Cannot find /tmp/file.log"Cannot find /tmp/file.log
)                = 26
exit(-1 <no return ...>
+++ exited (status 255) +++
```

Interesting, the executable is using `fopen` on `/tmp/file.log` with the `r` or *read only* parameter. 

We can manipulate the program if we create a symlink to `/etc/leviathan_pass/levithan6` and link it to `/tmp/file.log` the program won't know the difference between two the files because it's not sanitizing the input.

```console
leviathan5@leviathan:~$ ln -s /etc/leviathan_pass/leviathan6 /tmp/file.log
leviathan5@leviathan:~$ ./leviathan5
password_to_leviathan6
```

<details>
  <summary>Show Answer</summary>

  Username: leviathan6

  Password: YZ55XPVk2l
</details>

---

## Leviathan 6 -> 7
[https://overthewire.org/wargames/leviathan/leviathan7.html](https://overthewire.org/wargames/leviathan/leviathan7.html)

As always, let’s run `ls -la` and see what we have in our home directory.

```console
leviathan6@leviathan:~$ ls -la
total 28
drwxr-xr-x   2 root       root       4096 Nov 14  2014 .
drwxr-xr-x 172 root       root       4096 Jul 10 14:12 ..
-rw-r--r--   1 root       root        220 Apr  9  2014 .bash_logout
-rw-r--r--   1 root       root       3637 Apr  9  2014 .bashrc
-rw-r--r--   1 root       root        675 Apr  9  2014 .profile
-r-sr-x---   1 leviathan7 leviathan6 7484 Nov 14  2014 leviathan6
leviathan6@leviathan:~$ ./leviathan6
usage: ./leviathan6 <4 digit code>
```

As you can see the executable we are working with is asking us to input a 4 digit pin. We could try to brute force it using a shell script.

```console
leviathan6@leviathan:~$ mkdir /tmp/pyr0
leviathan6@leviathan:~$ nano /tmp/pyr0/brute.sh
#!/bin/bash

for a in {0000..9999}
do
~/leviathan6 $a
done

leviathan6@leviathan:/tmp/pyr0$ chmod +x brute.sh
leviathan6@leviathan:/tmp/pyr0$ ./brute.sh
...
...
...
$ whoami 
leviathan7
```

Another solution is to inspect the logic of the program. We can do that by trying to decompile or disassemble the program. We will do this with a nifty website called [Decompiler Explorer](https://dogbolt.org/).

First we need to get the file off the system. We can do this using `scp` (secure copy).

```console
scp -P 2223 leviathan6@leviathan.labs.overthewire.org:~/leviathan6 /home/kali/Downloads

leviathan6@leviathan.labs.overthewire.org's password:
leviathan6                             100%    0     0.0KB/s   00:00
```

Open a web browser and go to https://dogbolt.org/

![](https://i.imgur.com/GJjcjVR.png)

Upload the executable `leviathan6`

It will begin to disassemble the program as close as it can to the original source code.

![](https://i.imgur.com/gjVIUpc.png)

So we have an `if` condition being evaluated against a integer. As you can see here Hex-Rays was able to reveal to us the exact value that is being referenced which gave us the pin code.

Also you can see Ghidra was able to do the same but instead the value is in hexadecimal. If we use [RapidTables](https://www.rapidtables.com/convert/number/hex-to-decimal.html) again to convert this to decimal we would return `7123`.

```console
leviathan6@leviathan:~$ ./leviathan6 7123
$ whoami 
leviathan7
$ cat /etc/leviathan_pass/leviathan7
pass_to_leviathan7
```

<details>
  <summary>Show Answer</summary>

  Username: leviathan7

  Password: 8GpZ5f8Hze
</details>