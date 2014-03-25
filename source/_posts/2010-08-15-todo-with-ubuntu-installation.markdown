---
author: admin
comments: true
date: 2010-08-15 16:49:56+00:00
layout: post
slug: todo-with-ubuntu-installation
title: '#After ubuntu installation requirements Part I'
wordpress_id: 12
categories:
- Technology
tag:
- Linux
- Ubuntu
---

After installation of Ubuntu you may need to set up your system for some basic requirements. For these requirements you can follow these steps :<!--more-->

1 – Expand the Software Repository List

First of all, lets make Ubuntu “see” more packages. Go to the terminal and edit your sources.list with :

sudo gedit /etc/apt/sources.list

Here is the content of my sources.list which I think is quite complete to have all the necessary applications you could ever need. So delete the whole content of your sources list and replace it with the content of mine

Save it. Now import the necessary repositories keys to avoid “apt-get” crying about some missing keys, go to the terminal and type:

sudo apt-key adv --recv-keys --keyserver keyserver.ubuntu.com <key>

Get your system up to date with :

sudo apt-get update && sudo apt-get upgrade

Now all your programs will run on the last version.

2 – Anti-Virus

Windows equivalent : AVG AntiVirus, NAV, TrendMicro, F-Prot, Kaspersky, …

Ubuntu equivalent : ClamAV, Avast

ClamAV

sudo apt-get install clamav clamtk

Access it through System Tools → Virus Scanner .

Avast

wget http://files.avast.com/files/linux/avast4workstation_1.3.0-2_i386.deb && sudo dpkg -i avast4workstation_1.3.0-2_i386.deb

Access it through Accessories → avast! Antivirus .

3 – Essential tools for compiling from sources

sudo apt-get install build-essential checkinstall cdbs devscripts dh-make fakeroot libxml-parser-perl check avahi-daemon

4 – Java runtime environment

Java is a very important thing to install, now that many programs like Azureus need it to run. So type:

sudo apt-get install sun-java6-jre sun-java6-plugin equivs

5 – Ubuntu Control Panel

Ubuntu Tweak

Ubuntu Tweak allows you to tweak your system settings, all in one place. You can install new applications, customize your desktop settings, configure your startup applications, changing the system filetype association and many more tweaks in this single application.

sudo apt-get install ubuntu-tweak

Then access it through Applications → System Tools → Ubuntu Tweak

App Runner

App Runner is a small open source utility that makes it very easy to run any type of program/executable/script on any distro/OS that uses the nautilus file manager: Debian/Ubuntu/Super OS/Fedora/etc

wget http://hacktolive.org/files/app_runner/App_Runner_0.2.deb && sudo dpkg -i App_Runner_0.2.deb

Then right-click the file -> Scripts -> Run This App or Run This App (root)

6 – Multimedia

Windows equivalent : windows media player, real player, vlc, mplayer

Ubuntu equivalent : vlc, mplayer, helix player

To have Ubuntu playing all kinds of stuff, you need to install many codecs. So on the Terminal, type:

* Installing vlc and mplayer (plays almost everything):

sudo apt-get install vlc mplayer

* Common packs

sudo apt-get install non-free-codecs libxine1-ffmpeg gxine mencoder mpeg2dec vorbis-tools id3v2 mpg321 mpg123 libflac++6 ffmpeg libmp4v2-0 totem-mozilla icedax tagtool easytag id3tool lame nautilus-script-audio-convert mozilla-helix-player helix-player libmad0 libjpeg-progs libmpcdec3 libquicktime1 flac faac faad sox ffmpeg2theora libmpeg2-4 uudeview flac libmpeg3-1 mpeg3-utils mpegdemux liba52-dev

* Gstreammer 0.10

sudo apt-get install gstreamer0.10-ffmpeg gstreamer0.10-fluendo-mp3 gstreamer0.10-gnonlin gstreamer0.10-pitfdll gstreamer0.10-sdl gstreamer0.10-plugins-bad-multiverse gstreamer0.10-schroedinger gstreamer0.10-plugins-ugly-multiverse totem-gstreamer
sudo apt-get install python-nautilus meld python-svn python-configobj ipython
* More programs

sudo apt-get install gstreamer-dbus-media-service gstreamer-tools ubuntu-restricted-extras

* Enable dvd support

sudo aptitude install libdvdcss2 && sudo /usr/share/doc/libdvdread4/./install-css.sh

* Flash

sudo apt-get install gsfonts gsfonts-x11 flashplugin-nonfree

7. Tweak your eyecandy

Ubuntu 9.04 comes with compiz fusion effects OOTB but doesn’t offer a way to customize them.

In a terminal copy/paste this:

sudo apt-get install simple-ccsm

Now navigate to System → Preferences → Simple CompizConfig Settings Manager .

8 – Missing Windows software?? Run Windows softwares in Linux!!!

Run Windows Applications such as 7zip, Google Sketchup, AutoCAD, Dreamwaver, Flash MX, Fireworks MX, IE6, IE7, Safari, Itunes, Windows Media Player and many more…

Play Windows Games in Linux like Age Of Empires, Call Of Duty, Diablo, Fear, Fallout, Far Cry, Grand Theft Auto, Half Life, Halo, Hitman, Max Payne, Need For Speed, Prince Of Persia, Sim City Star Wars, The Simsworld of warcraft , Tomb Raider, Warcraft, World Of Warcraft, Counterstrike and many other can be played.

Install Playonlinux. It’s based on wine. Wine is a compatibility layer for running Windows programs in Linux.

sudo apt-get install wine playonlinux

9 – Clipboard Management

By Default in ubuntu when u copy something from an application and closes the application u will not be able to access it from the clipboard. And also when u copy severals text in serial u only have the last on available to you in the clipboard. To solve that install either of the following but Glipper is better because it supports plugins.

Glipper

sudo apt-get install glipper

Then right click ur panel → Add to Panel then drag Clipboard Manager to ur panel

Parcellite

sudo apt-get install parcellite

10 – Archiver/ Packing software

Windows equivalent : winrar, zip, 7zip

Ubuntu equivalent : tar, unrar, p7zip, arj, unace

It’s bad when you don’t have Internet on your computer/notebook, but you have to pack/unpack something but the file format isn’t recognized by the system. To prevent from this bad situation, you can install a bunch of packing software by typing this on the terminal:

sudo apt-get install unace rar unrar zip unzip p7zip-full p7zip-rar sharutils aish uudeview mpack lha arj cabextract file-roller
