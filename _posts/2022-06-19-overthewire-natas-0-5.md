---
layout: post
title: "OverTheWire Natas 0-5"
date: 2022-06-19 14:00:00
style: border
color: primary
tags: [Security, Overthewire, Web-Security]
description: OTW - Natas Walkthrough 0-5
---

These challenges are called *Wargames*. Wargames are similar to a CTF but are always ongoing. Typically, they are organized into levels that get progressively harder as you solve more of them. Wargames are an excellent way to practice for CTFs!

This set of challenges or *Wargames* is called [Natas](https://overthewire.org/wargames/natas/). Natas teaches the basics of serverside web-security.
Here is my walkthrough of each level. If you would like to jump to specific level then each level with be accompanied with the username and password. I will be appending consecutive levels as I have to time to post. Enjoy!

## Natas 0
[http://natas0.natas.labs.overthewire.org](http://natas0.natas.labs.overthewire.org)

In their website they give us the username and password for natas0.

> **Username:** natas0
> **Password:** natas0

---

## Natas 0 → 1 
[http://natas0.natas.labs.overthewire.org](http://natas0.natas.labs.overthewire.org)

It says it’s on this page, but we don’t see it, do we? 

![](https://i.imgur.com/oDDaBDv.png)

Right click anywhere on the page and select view the source. You’ll notice a comment:

```
<!--The password for natas1 is gtVrDuiDfck831PqWsLEZy5gyDz1clto -->
```

That's our password. Easy!

> **Username:** natas1
> **Password:** gtVrDuiDfck831PqWsLEZy5gyDz1clto

---

## Natas 1 → 2 
[http://natas1.natas.labs.overthewire.org](http://natas1.natas.labs.overthewire.org)

We need to view the source but we can't because right-clicking is blocked.

![](https://i.imgur.com/igPfPWF.png)

There are a couple of ways around this.

**Method #1**
The simplest is to use the keyboard shortcut `Ctrl` + `U`

**Method #2**
Download NoScript browser extension. *Essentially blocks javascript from executing.*
Now, you can right click anywhere on the page and select view the source. You’ll notice a comment:

```
<!--The password for natas2 is ZluruAthQk7Q2MqmDeTiUij2ZvWy2mBi -->
```

Fairly similar to the last challenge.

> **Username:** natas2
> **Password:** ZluruAthQk7Q2MqmDeTiUij2ZvWy2mBi

---

## Natas 2 → 3 
[http://natas2.natas.labs.overthewire.org](http://natas2.natas.labs.overthewire.org)

Nothing on the page?

![](https://i.imgur.com/Y9mfR7V.png)

- View the source

- Notice the `files/pixel.png`

```html
<body>
<h1>natas2</h1>
<div id="content">
There is nothing on this page
<img src="files/pixel.png">
</div>
</body>
```

- Navigate the the `files` directory

![](https://i.imgur.com/r1yOJbG.png)

- The site is vulnerable to something called ["Directory Traversal"](https://www.acunetix.com/blog/articles/directory-listing-information-disclosure/). Open `users.txt`. There you’ll find the password to the next level.

```
# username:password
alice:BYNdCesZqW
bob:jw2ueICLvT
charlie:G5vCxkVV3m
**natas3:sJIJNW6ucpu6HPZ1ZAchaDtwd7oGrD14**
eve:zo4mJWyNj2
mallory:9urtcpzBmH
```

> **Username:** natas3
> **Password:** sJIJNW6ucpu6HPZ1ZAchaDtwd7oGrD14

---

## Natas 3 → 4 
[http://natas3.natas.labs.overthewire.org](http://natas3.natas.labs.overthewire.org)

Nothing on the page?

![](https://i.imgur.com/iq6QsTo.png)

- View the source

```html
<body>
<h1>natas3</h1>
<div id="content">
There is nothing on this page
<!-- No more information leaks!! Not even Google will find it this time... -->
</div>
</body>
```

Nothing really here...other than a boastful clue...

"Not even Google will find it this time..." Eh?

What is Google famously known for doing?

Crawling / indexing websites of course...so let's do our own web crawling and see if we come up with anything.

To do this we need to use a tool called `gobuster`

- Gobuster scanning

```console
# gobuster dir -u http://natas3.natas.labs.overthewire.org/ -w /usr/share/wordlists/dirb/common.txt -U natas3 -P sJIJNW6ucpu6HPZ1ZAchaDtwd7oGrD14           
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://natas3.natas.labs.overthewire.org/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Auth User:               natas3
[+] Timeout:                 10s
===============================================================
2022/06/21 21:34:20 Starting gobuster in directory enumeration mode
===============================================================
/.htaccess            (Status: 403) [Size: 317]
/.htpasswd            (Status: 403) [Size: 317]
/.hta                 (Status: 403) [Size: 312]
/cgi-bin/             (Status: 403) [Size: 316]
/index.html           (Status: 200) [Size: 923]
/robots.txt           (Status: 200) [Size: 33] 
/server-status        (Status: 403) [Size: 321]
                                               
===============================================================
2022/06/21 21:35:28 Finished
===============================================================
```

Hmm `/robots.txt` looks promising.

```
User-agent: *
Disallow: /s3cr3t/
```

Another clue `/s3cr3t/`. Let's navigate to that folder.

![](https://i.imgur.com/6i5CcTA.png)

Open `users.txt`. There you’ll find the password to the next level.

```
natas4:Z9tkRkWmpt9Qr7XrR5jWRkgOU901swEZ
```

> **Username:** natas4
> **Password:** Z9tkRkWmpt9Qr7XrR5jWRkgOU901swEZ

---