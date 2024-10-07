---
layout: post
title: "Learning how to build the Linux kernel for the first time"
categories: [Linux, Kernel]
---

I'm diving in head-first to Linux and how it works, with a special emphasis on the kernel. While I've found several resources to help me along the way, I ultimately landed on ["Linux Kernel Programming (2nd ed.)"](https://www.amazon.com/Linux-Kernel-Programming-practical-synchronization/dp/1803232226) (LKP) by Kaiwan N Billimoria. This book is the most up-to-date resource I could find that teaches from my desired level of abstraction, teaching a mix of Linux command-line tricks along with information on how the kernel works.

The first practical exercise in the book is compiling the Linux kernel. Seems like a great place to start, especially considering I've never compiled the kernel before on my own! That was always a layer of complexity that I never interacted with, and even felt intimidated by.

To my delight, the process was fairly straighforward. I know this wasn't always the case, so I am quite happy stand on the shoulders of the giants who came before and created easier procedures for folks like me.

Here's the general process I followed, guided by LKP:

## Get the kernel source code and extract it somewhere

There are several ways to do this. I opted to get it from here: [The Linux Kernel Archives](https://www.kernel.org/). I downloaded and extracted the .tar.gz corresponding with the kernel version used in LKP so that I can follow along with the instruction.

## Start from a clean slate

Running the ```make mrproper``` command in the root source directory will reset its contents back to a fresh state, as if it was just downloaded. Very helpful for when you screw up a few times while learning and you need to roll back and try again.

## Build a .config file

Before compiling the kernel, I needed to create a **.config** file. This file is ultimately responsible for deciding which features are compiled into the kernel vs. left out.

There are a few ways to make this config file, but since my ultimate goal was to compile a kernel that would work on the same host machine (in a QEMU VM), I used the ```localmodconfig``` Make target:

1. Since I wanted to know which modules were currently running on my host system's current kernel version, I consulted the ```lsmod``` command, short for "list modules." These modules represent Linux Kernel Modules, which is apparently the primary method used for adding device drivers.

2. Following LKP's lead, I copied this list into a file and assigned it to a shell variable, ```LSMOD``` (the variable, not the command):

    First, put your modules list in an arbitrary file:
    
    ```bash
    lsmod > /tmp/current_mods
    ```

    Now you can use that file as a shell variable:
    ```bash
    make LSMOD=/tmp/current_mods localmodconfig
    ``` 
    
    This environment variable is used by Make when run against the ```localmodconfig``` target in order to fill out a preliminary .config file that is already tailored to your currently working Linux system. *As a novice to all of this, I can't overstate much I appreciate this relatively easy method of creating a config so I can continue to focus on actually compiling the kernel!*

