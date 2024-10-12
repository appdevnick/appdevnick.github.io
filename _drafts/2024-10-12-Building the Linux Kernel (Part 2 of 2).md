---
layout: post
title: Building the Linux Kernel (Part 2 of 2)
categories: Linux Kernel
page.image: /assets/images/bits.jpg
---

In [Part 1](https://nickskernelquest.com/posts/Building-the-Linux-Kernel-(Part-1-of-2)/) I looked at obtaining the kernel source code and setting up an initial configuration file. This file keeps track of any alterations or additions that I might want to add. Small examples include renaming the kernel version or disabling unneeded features that are on by default.

The next step entails building a kernel image based off of this config file, along with any supporting drive modules, that will be loaded into memory at boot time.

## Build the kernel

Thanks to the work of others, actually "building" the kernel is often as simple as running the ```make``` command. When it is run without any target arguments, it will run the first target it finds in the Makefile, which for the Linux kernel is "all."

When you ```make``` the output will be:
1. "vmlinux", the **uncompressed** kernel image file. This is not the one your systemn will load into memory. Instead, it acts as a useful debug target when something goes wrong with your kernel because it has debug symbols in it. It's a big file, much too big to efficiently load at runtime.
2. "bzImage", the **compressed** kernel image. This is the artifact that will be used at boot.
3. "modules", which are are the features marked as "M" (rather than "*" or " ") in the ```make menuconfig``` step. Those features get compiled into individual modules rather than getting backed into the kernel image statically.