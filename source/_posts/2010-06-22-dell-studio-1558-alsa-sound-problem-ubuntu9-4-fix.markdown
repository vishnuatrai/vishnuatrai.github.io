---
author: admin
comments: true
date: 2010-06-22 16:36:57+00:00
layout: post
slug: dell-studio-1558-alsa-sound-problem-ubuntu9-4-fix
title: Dell Studio - 1558 ALSA sound problem ubuntu9.4 fix
wordpress_id: 9
categories:
- Linux
- Ubuntu
---

I had the problem and the Laptop, Dell Studio 1558:
just exec as root: "nano /etc/modprobe.d/alsa-base.conf

and add this line:

options snd-hda-intel model=dell-m6 <!--more-->

it works for me. modeprobe.conf files are empty.
if modprob.conf files are not empty then add this line to
options snd-hda-intel model=dell-m6

it will work

I had the problem and the Laptop, Dell Studio 1558:just exec as root: "nano /etc/modprobe.d/alsa-base.conf"
and add this line:options snd-hda-intel model=dell-m6
it works for me. modeprobe.conf files are empty.

if modprob.conf files are not empty then add this line tooptions snd-hda-intel model=dell-m6 it will work
