---
layout: post
title: "OverTheWire: Bandit 26-27"
date: 2021-04-12 23:24:03
tags: [Security, Overthewire, Linux, Pentesting]
style: border
color: primary
description: OTW - Bandit Walkthrough 26-27
---

Welcome to another post on our series of [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)! This post covers my walkthrough of levels 26-27. If you would like to jump to specific level then each level with be accompanied with the username and password. I will be appending consecutive levels as I have to time to post. Enjoy!

## Bandit 26

[http://overthewire.org/wargames/bandit/bandit26.html](http://overthewire.org/wargames/bandit/bandit26.html)

Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not **/bin/bash**, but something else. Find out what it is, how it works and how to break out of it.

First let's *ssh* into the bandit25 server and have a look in the home directory:

```console
bandit25@bandit:~$ ls
bandit26.sshkey
bandit25@bandit:~$
```

Okay so there's a RSA private key and this must be why they stated  "should be fairly easy..." to connect. For kicks and giggles let's try logging into bandit26 via *ssh*

```console
...
--[ More information ]--

  For more information regarding individual wargames, visit
  http://www.overthewire.org/wargames/

  For support, questions or comments, contact us through IRC on
  irc.overthewire.org #wargames.

  Enjoy your stay!

  _                     _ _ _   ___   __
 | |                   | (_) | |__ \ / /
 | |__   __ _ _ __   __| |_| |_   ) / /_
 | '_ \ / _` | '_ \ / _` | | __| / / '_ \
 | |_) | (_| | | | | (_| | | |_ / /| (_) |
 |_.__/ \__,_|_| |_|\__,_|_|\__|____\___/
Connection to localhost closed.
bandit25@bandit:~$
```

Interesting! It kicks us out of the session. The challenge mentions that the shell for bandit26 isn't `/bin/bash/` so that's a good place to start looking. 

There are several places within a Linux system to check what shell environment is present but one of the prime ways to do this is to check the `/etc/passwd` file. This will tell us what default shell environment is being called for each user on the system.

```console
bandit25@bandit:~$ cat /etc/passwd | grep bandit26
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
bandit25@bandit:~$
```

What is `/usr/bin/showtext`? Let's `cat` that file to see.

```console
bandit25@bandit:~$ cat /usr/bin/showtext
#!/bin/sh

export TERM=linux

more ~/text.txt
exit 0
bandit25@bandit:~$
```

So it calls the `more` utility on a file called `text.txt`, which is the bandit26 banner ASCII art we see, then exits.

Okay so the `more` command is relative to the size of our terminal window. This means if we make our terminal smaller than the ASCII art then `more` will wait for us to press space to continue rather than just displaying it and exiting. In short, make your terminal as small as possible then *ssh* in.

![](https://i.imgur.com/iefSca9.png)

Okay now that we've got `more` paused for lack of a better phrase, what do we do now? I'll be honest this is where I was stuck as well but apparently within `more` we can execute a hotkey commands. One of them is `vim`. I know what you're thinking...I hate vim but I promise this next part is really cool. 

So you press *v* on the keyboard to open `vim` and you can rescale your window so that you're actually able to see now. 

![](https://i.imgur.com/8xMDB2Q.png)

If you’re unfamiliar with `vim`, make sure you press escape to enter command mode.

So that we have `vim`, we can open a shell up using the `:shell` command. That said, **remember** if we enter a shell then it will just run the showtext bash script and exit again. So how do we break out of this? 

1. First we set the shell we want. In this case a *bash* shell
```console
:set shell=/bin/bash
```

2. Now we start our shell
```console
:shell
bandit26@bandit:~$ whoami
bandit26
bandit26@bandit:~$
```

![](https://i.imgur.com/cR7CJRG.png)

Yay! We got a shell! Now if we want the password for bandit26 we just `cat` the password file.

```console
bandit26@bandit:~$ cat /etc/bandit_pass/bandit26
5czgV9L3Xx8JPOyRbXh6lQbmIOWvPT6Z
```

**NOTE: DO NOT exit the bandit26 just yet! You'll get kicked out again! Check the bandit27 walkthrough for more info.** 

> **Username:** bandit26
> **Password:** 5czgV9L3Xx8JPOyRbXh6lQbmIOWvPT6Z

---

## Bandit 27

[http://overthewire.org/wargames/bandit/bandit27.html](http://overthewire.org/wargames/bandit/bandit27.html)

Okay hopefully you haven't exited out of your shell within bandit26. If you have then you'll have to redo the previous level before starting this one. 

Good job getting a shell! Now hurry and grab the password for bandit27!

As usual let's check the homedirectory.

```console
bandit26@bandit:~$ ls
bandit27-do  text.txt
```

Looks like another setuid binary misconfigured. Let's see what it does.

```console
bandit26@bandit:~$ ./bandit27-do
Run a command as another user.
  Example: ./bandit27-do id
```

Okay so we can execute whatever command we want as the user bandit27. This means we can bypass use permissions and  just read the password file directly for user bandit27. 

```console
bandit26@bandit:~$ ./bandit27-do cat /etc/bandit_pass/bandit27
3ba3118a22e93127a4ed485be72ef5ea
bandit26@bandit:~$
```

> **Username:** bandit27
> **Password:** 3ba3118a22e93127a4ed485be72ef5ea

---