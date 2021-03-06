---
layout: post
title:  "Debug kernel by qemu!"
date:   2016-05-01 12:59:00
categories: Kernel Virtualization
tags: Kernel Virtualization
---
### Steps:

- creates a configuration based on the defaults for your architecture

```sh
# make defconfig
```

- define extra configuration if required. 

```sh
# vim .config      --> edit by yourself
# make menuconfig  --> which can help to add some configuration automatically 
```

- enable debug info option

Kernel debugging->Compile the kernel with debug info"

```sh
    [*] KGDB: kernel debugging with remote gdb
        Method for KGDB communication (KGDB: On generic serial port (8250)) --->
    [*] KGDB: Thread analysis
    [*] KGDB: Console messages through gdb
```

- compile kernel

```sh
# make -j<n>
```

- confirm the result of compile

```sh
[root@localhost linux-debug]# pwd
/mnt/extra_disk/osc/linux-debug
[root@localhost linux-debug]# ll arch/x86/boot/bzImage
-rw-r--r-- 1 root root 6414240 Jun 17 17:10 arch/x86/boot/bzImage
```

- start qemu with the compiled kernel

```sh
#qemu-system-x86_64 -s -S -kernel /mnt/extra_disk/osc/linux-debug/arch/x86/boot/bzImage -hda linux-0.2.img -append "root=/dev/sda console=ttyS0" -m 512M
```

Note:
-s option makes Qemu listen on port tcp::1234; -S option makes Qemu stop execution until you give the continue command

- start gdb in another terminal

```sh
# gdb /mnt/extra_disk/osc/linux-debug/arch/x86/boot/vmlinux
(gdb) target remote localhost:1234
Remote debugging using localhost:1234
0x0000000000000000 in irq_stack_union ()
(gdb) b start_kernel
Breakpoint 1 at 0xffffffff81f42c00
(gdb) c
(gdb) list   --> browse the current line's codes
```

- Q&A

Maybe you'll met "gdb - Remote 'g' packet reply is too long", Please update gdb from source codes and comment the statments in 'gdb/remote.c' like below,

```sh
 //if (buf_len > 2 * rsa->sizeof_g_packet)
 //  error (_("Remote 'g' packet reply is too long: %s"), rs->buf);
```

**There is but one secret to sucess---never give up!**
