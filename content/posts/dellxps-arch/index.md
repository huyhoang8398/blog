---
title: "Why I made the switch from Mac to Linux"
date: 2020-01-13T19:45:40+07:00
authors: ["kn"]
tags: ["linux", "dell", "xps", "archlinux"]
keywords: ["linux", "dell", "xps", "archlinux"]
description: "Why I made the switch from Mac to Linux"
showFullContent: false
cover: "/posts/dellxps-arch/images/cover.png"
---

## Introduction

I have been a huge Mac fan and power user since I started in IT in 2015.
But a few months ago—for several reasons — I made the commitment to shift to Linux as my daily driver.
This isn't my first attempt at fully adopting Linux, but I'm finding it easier than ever.
Here is what inspired me to switch.

## Why Im falling in love with macOS

First of all, in 2015 I had my very first macOS laptop which is an Macbook air 13 inch model.
Before that, I use Linux as my daily driver.
The macbook air is light, fast, the battery life is awesome.
It came with macOS El Capitan at that moment - which is ultrafast comparing to window and linux.

## Macbook is not the best ultrabook nowaday

Recently I have a gift which is a new Macbook Pro 2017 without touchbar version.
The design is great, very beautiful screen, the trackpad is good and also the battery life.
But It shipped with the Butterfly gen 2 which has alot of issues, and Apple said that they did fix these problem in 2nd generation but...

My keyButterfly keys use a butterfly mechanism that's different from the scissor mechanism used for traditional keyboards.
It's called a butterfly mechanism because the components underneath the key resembles a butterfly's wings, with a hinge in the center rather than overlapping like a pair of scissors.

Apple swapped to a butterfly mechanism to make a thinner keyboard, which is possible because each key moves less when pressed so less space is needed.
The keyboard provides a satisfying amount of travel and stability when each key is pressed, but unfortunately, the thin butterfly mechanism can get jammed up with crumbs, dust, and other particulates, resulting in keys that don't press properly, keys that skip keystrokes, or keys that repeat letters.
board

![Traditional vs Butterfly keyboard](/posts/dellxps-arch/images/scissor-vs-butterfly.jpg)

Keyboard failure is an in Apple's notebooks because replacing the keyboard requires the entire top assembly of the computer to be replaced, which is not a cheap repair.

Unfortunately, I fall into that trouble 3 times just 2 weeks after purchase.
So I decided to trade it to a new dell xps 13 inch 2019 model.

## Dell XPS - my new favourite ultrabook

The dell xps is slightly thicker than the macbook but they are both lightweight and beautiful.
The performance is solid, though not industry-leading by any means.
All the XPS 13 laptops use eighth-generation Core CPUs.
Comparing to my old Macbook, the performance is much better. More cores haha \m/

## Dell XPS + Archlinux > MacOS

So I decided to install Archlinux on this machine thus Im a fan of this Linux Distribution.
I also have a PC running Arch powered by Ryzen 🎉🎉🎉

There was only a few issue on this machine, first of all is the `coil whine sound` :(, I still have no idea how to fix it, not that loud, but weird on a expensive laptop like this.
The other thing is `screen flickering`.
Panel Self Refresh (PSR), a power saving feature used by Intel iGPUs is known to cause flickering in some instances.
A temporary solution is to disable this feature using the kernel parameter i915.enable_psr=0

## Result

Im using Archlinux + KDE, used to be a fan of i3wm but I want to give a try on KDE.

![Dell XPS Archlinux + KDE](/posts/dellxps-arch/images/cover.png)

Thus this have only been used for 2 days so need more time for review. I will update in some future posts.
