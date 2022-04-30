---
layout: post
title: "Steganography with Steghide"
date: 2021-08-16 19:21:06
tags: [Security, Cryptography, Steganography, Forensics]
style: border
color: primary
description: Steghide is a stego program that is able to hide data in various kinds of image and audio-files.
---

Today I will show you a steganography method on how to hide sensitive data inside image and audio files using the steghide utility. The author of this tool is Stefan Hetzl.

![A picture of Mona Lisa with a magnifying glass peering into ones and zeros.](https://i.imgur.com/js0Q0An.jpg)

## History of Steganography

Did you know that the Roman Empire would select a slaves then shave their heads and messages were tattooed onto the skin. When the messenger’s hair grew back, he or she was dispatched. The receiver shaved the messenger’s head again and read the message. 

## But wait dude? What is steganography?

Now you've probably heard of cryptography but what is steganography? Without getting too deep into discussion, crytography is a means of encrypting known data to protect it whereas steganography is the art of concealing any occurence of data in plain sight. If you want to know more info about the differences between the two I suggest reading this [article](https://sectigostore.com/blog/what-is-the-difference-between-steganography-vs-cryptography/). Open a terminal and let's get started!

## Installation

- Debian-based systems
  - `sudo apt install steghide`

- Arch Linux-based systems
  - `sudo pacman -S steghide`

## Hiding/Embedding a file

This command will embed the file secret.txt in the cover file picture.jpg.

```console
$ steghide embed -cf picture.jpg -ef secret.txt
  Enter passphrase:
  Re-Enter passphrase:
  embedding "secret.txt" in "picture.jpg"... done
```

Let's break this down:
- `embed` specifies that we are embedding or hiding a file
- `-cf` specifies the cover file
- `-ef` specifies the embedded file or data we want to hide

## Unhiding/Extracting a file

```console
$ steghide extract -sf picture.jpg
  Enter passphrase:
  wrote extracted data to "secret.txt".
```

If the passphrase is correct, the contents of the original file secret.txt will be extracted from the stego file picture.jpg.

Let's break this down:
- `extract` specifies that we are extracting or unhiding a file
- `-sf` specifies the stego file or artifact we are trying to extract data from

## How to view the file before extracting?

If you have received a file that contains embedded data and you want to get some information about it before extracting it, use the info command:

```console
  $ steghide info received_file.wav
  "received_file.wav":
    format: wave audio, PCM encoding
    capacity: 3.5 KB
  Try to get information about embedded data ? (y/n) y
  Enter passphrase:
    embedded file "secret.txt":
      size: 1.6 KB
      encrypted: rijndael-128, cbc
      compressed: yes
```

It will output some formatting information about the file then you will be asked if steghide should try to get information about the embedded data. If you answer with yes you must input a passphrase. Steghide will then try to extract the embedded data with provided passphrase and upon successfull completion will print some information about the data.

## How to use other encryption methods within steghide?

`steghide --encinfo`

The command above displays the list of supported encryption algorithms and modes that can be used.

```console
$ steghide --encinfo
encryption algorithms:
: ...
cast-128: cbc cfb ctr ecb ncfb nofb ofb
gost: cbc cfb ctr ecb ncfb nofb ofb
rijndael-128: cbc cfb ctr ecb ncfb nofb ofb
twofish: cbc cfb ctr ecb ncfb nofb ofb
arcfour: stream
cast-256: cbc cfb ctr ecb ncfb nofb ofb
loki97: cbc cfb ctr ecb ncfb nofb ofb
rijndael-192: cbc cfb ctr ecb ncfb nofb ofb
saferplus: cbc cfb ctr ecb ncfb nofb ofb
wake: stream
des: cbc cfb ctr ecb ncfb nofb ofb
rijndael-256: cbc cfb ctr ecb ncfb nofb ofb
serpent: cbc cfb ctr ecb ncfb nofb ofb
xtea: cbc cfb ctr ecb ncfb nofb ofb
blowfish: cbc cfb ctr ecb ncfb nofb ofb
enigma: stream
rc2: cbc cfb ctr ecb ncfb nofb ofb
tripledes: cbc cfb ctr ecb ncfb nofb ofb
```

Now that we have a list of encryption methods to choose from, let's try to use the rijndael-256 method.

```console
$ steghide embed -cf picture.jpeg -ef secret.txt -e rijndael-256
  Enter passphrase: 
  Re-Enter passphrase: 
  embedding "secret.txt" in "picture.jpeg"... done
```

We can now view the info on the file to verify the specified encryption method was used.

```console
  $ steghide info picture.jpeg
  "picture.jpeg":
    format: jpeg
    capacity: 868.0 Byte
  Try to get information about embedded data ? (y/n) y
  Enter passphrase:
    embedded file "secret.txt":
      size: 8.0 Byte
      encrypted: rijndael-256, cbc
      compressed: yes
```

## Want know more about Steghide?

If you want to know more options which is available for steghide, simply navigate to man page.

`steghide --help`

or

`man steghide`
