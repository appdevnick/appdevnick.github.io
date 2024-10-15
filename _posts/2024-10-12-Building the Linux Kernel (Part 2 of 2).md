---
layout: post
title: Building the Linux Kernel (Part 2 of 2)
categories: Linux Kernel
page.image: /assets/images/bits.jpg
---

In [Part 1](https://nickskernelquest.com/posts/Building-the-Linux-Kernel-(Part-1-of-2)/) I looked at obtaining the kernel source code and setting up an initial configuration file. This file keeps track of any alterations or additions that I might want to add. Small examples of such alterations might include renaming the kernel version or disabling unneeded features that are on by default.

The next step entails building a kernel image based off of this config file, along with any supporting drive modules, that will be loaded into memory at boot time.

## Build the kernel

Thanks to the work of others, actually "building" the kernel is often as simple as running the ```make``` command. When it is run without any target arguments, it will run the first target it finds in the Makefile, which for the Linux kernel is "all."

Now you run ```make all``` or just ```make``` and wait for the compiler to complete. "All" is the top listed target in the Makefile and thus will be chosen by default if not target is specified):
1. **vmlinux**, the ***uncompressed*** kernel image file. This is not the one your system will load into memory. Instead, it acts as a useful debug target when something goes wrong with your kernel because it has debug symbols in it. It's a big file, much too big to efficiently load at runtime.
2. **bzImage**, the ***compressed*** kernel image. This is the artifact that will be used at boot.
3. **modules**, which are are the features marked as "M" in the ```make menuconfig``` step. Those features get compiled into individual modules rather than getting packed into the kernel image statically.

### Solving Common Build Errors

Building the kernel from source doesn’t always go smoothly, especially for beginners. While the `make` command usually works well, here are some common errors I encountered and how I solved them:

1. **Missing Dependencies**  
   Install the necessary packages with:
   ```bash
   sudo apt-get install build-essential libncurses-dev bison flex libssl-dev libelf-dev```

2. **Cert issue on Ubuntu**
    [LKP](https://www.amazon.com/Linux-Kernel-Programming-practical-synchronization/dp/1803232226/) as well as numerous threads online point to a potential build failure under Ubuntu (and downstream) distributions.

    The error has to do with certs, and you can solve it by entering the following two commands, run from the root source directory:

    ```scripts/config --disable SYSTEM_TRUSTED_KEYS;```
    
    and

    ```scripts/config --disable SYSTEM_REVOCATION_KEYS```

    See [this discussion](https://askubuntu.com/questions/1501539/kernel-6-7-2-issues).

## Install Kernel Modules

Remember, anything you marked as "M" as ```make menuconfig``` was compiled as a separate module, so now you have to make sure and install them in the right directory on your system.

When you boot into your new kernel, it will need to be able to find the modules you configured it to use.

Here's the command: ```sudo make modules_install```

This will automatically install all necessary modules to ```/lib/modules/$(uname -r)/```. 

The ```$(uname -r)``` part means that you are instructing the shell to resolve the value of ```uname -r``` (the name of your current kernel) and insert it into the corresponding part of the directory path.

## Initramfs preparation

Initramfs stands for "initial RAM filesystem," which is exactly what it sounds like: When the system boots up and has no idea whether disks are present and how to talk to them, it relies on initramfs to already be present in main memory and ready to provide the kernel with essential drivers and other necessities for booting the system and finally getting access to the disk(s).

To prepare initramfs, enter this command in the root source directory: ```sudo make install```

Assuming this worked for you, then congratulations, you should be ready to boot into your new kernel. All that remains is to enter the Grub boot-loader menu and choose the correct entry!

## It's ok if this doesn't work for you right away

Linux is endlessly fascinating, but that can sometimes make newcomers feel like they are way out of their depth when things don't go to plan.

I chose brevity over exhaustive detail in this guide. Like the rest of this site, it exists primarily as a document of my own learning. If you see anything wrong or ill-advised herein, please reach out. 

Thank you for reading!