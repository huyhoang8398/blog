---
title: "Xf86 Video Intel"
date: 2020-03-21T17:59:52+07:00
tags: ["Linux", "Intel", "graphic", "blog"]
cover: "/posts/xf86-video-intel/img/cover.jpg"
keywords: ["Linux"]
showFullContent: false
---

## Why not XF86-Video-Intel ?

It is a DDX driver (which provides 2D acceleration in Xorg) but It is not recommended to install nowaday.
Because this package is no longer needed and also causes screen tearing.
And also the generic `xf86-video-modesetting` DDX driver included in xorg-server seems to be better than most vendor specific xf86-video- drivers.

Some ([Debian & Ubuntu](https://www.phoronix.com/scan.php?page=news_item&px=Ubuntu-Debian-Abandon-Intel-DDX), [Fedora](https://www.phoronix.com/scan.php?page=news_item&px=Fedora-Xorg-Intel-DDX-Switch), [KDE](https://community.kde.org/Plasma/5.9_Errata#Intel_GPUs)) recommend not installing the xf86-video-intel driver, and instead falling back on the modesetting driver for Gen4 and newer GPUs (GMA 3000 from 2006 and newer). 

## No xbacklight?

X86-video-intel provides the backlight property itself; such a property is not supplied by the kernel.
But we can use [acpilight](https://www.archlinux.org/packages/?name=acpilight) to set the display backlight with xbacklight. Add the following udev rule and your user to the video group:
```
/etc/udev/rules.d/90-backlight.rules
```
```
SUBSYSTEM=="backlight", ACTION=="add", \
  RUN+="/bin/chgrp video /sys/class/backlight/%k/brightness", \
  RUN+="/bin/chmod g+w /sys/class/backlight/%k/brightness"
```
It might be necessary to supply the acpi_backlight=vendor kernel parameter, see [backlight](https://wiki.archlinux.org/index.php/Backlight).

The original "xbacklight" sets the display brightness of the current $DISPLAY, whereas "acpilight" ignores $DISPLAY completely and sets the brightness of the first device found in /sys/class/backlight by default.
On systems with multiple panels, they might differ.

Use xbacklight -list to see the full list of available devices that can be
controlled; then use the -ctrl flag to control them individually.

"acpilight" can always query, but might not necessarily be allowed to set
the brightness of all listed devices depending on the current user and
permissions. 

"acpilight" doesn't fade: the requested brightness is set immediately by
default. For smooth transitions, specify the number of fading steps (using
-steps) or, more conveniently, the fading frame rate (-fps).