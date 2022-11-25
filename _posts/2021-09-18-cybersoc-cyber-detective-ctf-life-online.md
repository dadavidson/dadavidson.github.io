---
layout: post
title:  "CyberSoc | Cyber Detective CTF: Life Online"
date:   2021-09-17 23:30:00
tags: [Security, CyberSoc, OSINT]
style: border
color: primary
description: "Cyber Detective CTF: Life Online Writeup"
---

[CyberSoc Detective CTF](https://ctf.cybersoc.wales/) is a platform that contains many OSINT-based challenges. It was created by the [Cyber Society at Cardiff University](https://cybersoc.wales/about).

![](https://i.imgur.com/nJMDhzV.png)


In this post, I will be covering my write-up for the Life Online challenges. 

*NOTE: I do not have the flag to the challenge **leaveamessage**. In fact, at the time of this writing, I found out from the creators via their Facebook group that the challenge was broken and had several inconsistencies making the challenge not completable. They are currently working on a fix.*

---

## voteforme

200 points

![](https://i.imgur.com/HaK1GCh.png)

We're given Mr. James Markson's twitter profile so let's see if we can deduce from his profile his political affiliation.

![](https://i.imgur.com/0jcbH1p.png)

After searching down his feed we discover him retweeting something from Barrack Obama (Democrat).

![](https://i.imgur.com/kty7ND7.png)

Adding to that, we see him retweeting Ron Wyden essentially expressing his disdain for Donald Trump (Republican).

![](https://i.imgur.com/tFgFJfH.png)

Suffice to say James is a Democrat.

---

## growingup

200 points

![](https://i.imgur.com/E8flNS4.png)

Looking back at James's Twitter account, we see some strange almost cipher-like wording.

`Born in ///purple.pulse.force, raised in ///push.asking.barn.`

![](https://i.imgur.com/01eQtt1.png)

After browsing through more of his tweets, James references an app called [what3words](https://what3words.com/about).

![](https://i.imgur.com/gtXLf3s.png)

Essentially the tool allows people to use **3 words** to encode a person's precise location on their online map tool. Let's put in the phrases found on his Twitter before.

Born in...**Bristol** 
![](https://i.imgur.com/sDw8yKZ.png)

Raised in...**York**
![](https://i.imgur.com/1WUg6Fe.png)

---

## choochoo

200 points

![](https://i.imgur.com/0DsBdFt.png)

Amongst two tweets I was able to discern the answer to this challenge. For one, the train James usually boards on his way home is pretty consistently late. 

![](https://i.imgur.com/2OZ4pvw.png)

Secondly, he tweets a picture of the train station he departed from.

![](https://i.imgur.com/VmAvAUS.png)

Upon reverse image searched the picture of the train station I discovered the name of the train station to **Cardiff Central Railway Station** which is located obviously in **Cardiff**.

![](https://i.imgur.com/IY6Qe7o.png)

---

## suntan

200 points

![](https://i.imgur.com/EyzSwn9.png)

Amongst James's **Twitter likes** that he liked a tweet from an associate named Sarah Luxton(*Twitter: @sarah_luxton*). This tweet details a picture of what perceives to be her upcoming vacation in two weeks and the location thereof.

![](https://i.imgur.com/eXX9O5W.png)

Again I did a reverse image search in Google.

![](https://i.imgur.com/xofXK0p.png)

The location is **Perth**.

---

## wagthetail

200 points

![](https://i.imgur.com/7XavhKH.png)

Inspecting Sarah's profile she mentions, "Buster favorite place" and then provides some latitude longitude coordinates. I'm assuming "Buster" is her dog name.

![](https://i.imgur.com/WzBxQpf.png)

Cross-referencing the coordinates with Google Maps, we have a pin on a bridge at 2 Bridge Street, Brecon LD3 9AH, UK

![](https://i.imgur.com/UnWqgVm.png)

Diving into Google Streetview...

![](https://i.imgur.com/Rdk5UJi.jpg)

The answer for this challenge is simply **Brecon**.

---

## narcissism

200 points

![](https://i.imgur.com/AIJysVX.png)

Looking back again at James Markson's Twitter likes I stumbled upon a bold but stupid tweet by another associate by the name of George Watson(*Twitter: @GeorgeWatson428*). He thinks his password is "...secure and unbreakable!"

![](https://i.imgur.com/zir6HE8.png)

His password is encoded in base64 which can easily be decrypted using the following command:

```console
# echo aW1hbWF6aW5nMTIz | base64 --decode

imamazing123
```

---

## proppedup

200 points

![](https://i.imgur.com/hFs0IR1.png)

On George’s Twitter profile, we find another target named Pearce Ress(*Twitter: @PearceRess*). Pearce casually mentions George left his debit card laying around and states he left George's credit card on his desk. Also provides a proof photo of George Watson's desk.

![](https://i.imgur.com/6DnVZ2H.png)

I'm still puzzled by what the numbers mean but the answer was **Brown Silver**.

---

## bluengreen

250 points

![](https://i.imgur.com/X2IvKqY.png)

Circling back to Mr. James Markson's Twitter I noticed a small piece of text slipping out from behind his profile photo.

![](https://i.imgur.com/0jcbH1p.png)

Blowing the banner up in full size reveals a flag: **icanseeyou**

![](https://i.imgur.com/ioz6obV.jpg)

---

## clockingout

250 points

![](https://i.imgur.com/brRwQGD.png)

In one of his tweets, James mentions how it was a tough 8 hour day of work. The timestamp on the post reflects 4:02 PM, which subtracting 8 hours from then means he started work at 8:00 am.

![](https://i.imgur.com/zqA6972.png)

Twitter shows the tweet timestamps based upon the viewer's location or the original poster's location. Converting the timestamps from the US to UK time resulted in 14:00.

![](https://i.imgur.com/1PI5dab.png)

---

## meme

250 points

![](https://i.imgur.com/0vMrZV9.png)

This one was really easy as I had already seen through my enumeration of each target the text message conversation referencing a meme and a Software Access Code.

![](https://i.imgur.com/CH1BA6C.png)

Answer was **CTF3404X71**

---

## partytime

400 points

![](https://i.imgur.com/UsufQnw.png)

Pearce tweets he's hosting a party tagging @sarah_luxton @Sophjones77 @GeorgeWatson248 @jenmp7

![](https://i.imgur.com/4sYvBzu.png)

I used a combination of tools and compared the results to see if anything matched.

[Traveline](https://www.traveline.info) returned the following:

![](https://i.imgur.com/G2SWrhc.png)

I also used Google Maps and selected the Public Transport option:

![](https://i.imgur.com/tEtElui.png)

![](https://i.imgur.com/xy70yzr.png)

Looks like the best route is bus 58.

---

## leaveamessage

1000 points

![](https://i.imgur.com/5YXRQhG.png)

Still trying to solve it...