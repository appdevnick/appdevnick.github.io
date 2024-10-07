---
layout: post
title: Building the Linux Kernel (Part 1 of 2)
categories: Linux Kernel
page.image: /assets/images/bits.jpg
---
I'm diving headfirst into Linux, with a special emphasis on the kernel. While I found several resources to guide me, I ultimately chose ["Linux Kernel Programming (2nd ed.)"](https://www.amazon.com/Linux-Kernel-Programming-practical-synchronization/dp/1803232226) (LKP) by Kaiwan N Billimoria. This book is the most up-to-date resource I could find that matches my desired level of abstraction, offering a mix of Linux command-line tricks alongside insights into kernel internals.

The first practical exercise in the book is compiling the Linux kernel—an excellent starting point, especially since I've never done this on my own before. Kernel compilation has always seemed like a layer of complexity I avoided, even finding it intimidating.

To my delight, the process was relatively straightforward. I know it wasn't always this way, so I’m grateful to stand on the shoulders of those who simplified the process for learners like me.

Here's the general process I followed, guided by LKP:

## Get the kernel source code and extract it

There are several ways to do this. I opted to download it from [The Linux Kernel Archives](https://www.kernel.org/). I chose the .tar.gz file corresponding to the version used in LKP to follow along seamlessly.

## Start from a clean slate

Running the ```make mrproper``` command in the root source directory resets the contents to a fresh state, as if it were just downloaded. This is useful when you make mistakes while learning and need to start over.

## Build a .config file

Before compiling the kernel, I needed to create a **.config** file. This file determines which features are compiled into the kernel and which are left out.

There are multiple ways to create this file, but since my goal was to compile a kernel for my current host machine (in a QEMU VM), I used the ```localmodconfig``` Make target:

1. I wanted to see which modules were currently running on my host system, so I used the ```lsmod``` command (short for "list modules"). These modules represent Linux Kernel Modules (LKMs), which are the primary mechanism for adding device drivers.

2. Following LKP’s guidance, I saved this list to a file and assigned it to a shell variable, ```LSMOD``` (the variable, not the command):

    First, save your module list to a file:
    
    ```bash
    lsmod > /tmp/current_mods
    ```

    Then, use that file as a shell variable when running Make:
    ```bash
    make LSMOD=/tmp/current_mods localmodconfig
    ``` 
    
    This environment variable is used by Make when running the ```localmodconfig``` target to generate a preliminary .config file tailored to your current system. *As a novice, I really appreciated how this simplified the process, allowing me to focus on the kernel compilation itself!*

3. With the .config file generated, I reviewed it to confirm any last tweaks, including giving my custom kernel a unique name so that I could later verify that I was running my own build using the ```uname -mrs``` command.

    I ran ```make menuconfig```, which presented me with a GUI where I could make adjustments. I made two changes:

    1. **Enable runtime kernel configuration extraction:** In the "General setup" section, I set an option to always-on by selecting the asterisk (*) option.
        
        a. After enabling this, a new option appeared just below, allowing me to mount the active .config file to the virtual file system at ```/proc/config.gz```, which I also set to always-on.

        ![Image](/assets/images/config_vfs.jpg)
    
    2. **Change the kernel name:** Still in "General setup," under the "Local version" entry, I gave my local version string as "-zombiedust-three." *(You can probably guess how -one and -two went during earlier attempts!)*

        ![Image](/assets/images/rename_kernel.jpg)

## Time to take a break

LKP wisely ends the chapter here, giving newcomers like me a cognitive breather. There’s already a lot to absorb. The book goes into much more detail about what happens behind the scenes when running these Make targets, along with many other tidbits that are best understood from [the book itself](https://www.amazon.com/Linux-Kernel-Programming-practical-synchronization/dp/1803232226).

In Part 2, I’ll continue following LKP’s flow and hopefully finish my first kernel compilation.