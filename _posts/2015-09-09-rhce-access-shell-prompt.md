---
layout: post
title: RHCSA/RHCE RHEL7- Access a shell prompt and issue commands with correct syntax
comments: true
---

While preparing for RHCE RHEL 6, I came across [this website](https://oracle-base.com/articles/linux/rhcsa-and-rhce-6) which does an easy to follow  cheat sheet of sort for each topics under the RHCE/RHCSA Exam objectives. I was hugely impressed and wanted to do something similar. This is my attempt at replication the same for RHCE/RHCSA RHEL 7.

Now to the topic. Anyone attempting RHCE should already know to launch a terminal to access the shell and run commands. If you are really that fresh to Linux, among the many many resources available online I recommend Daniel Robin's [Linux Fundamental series](http://www.funtoo.org/Category:Linux_Core_Concepts) and [Introduction to Linux](https://www.edx.org/course/introduction-linux-linuxfoundationx-lfs101x-2) by the Linux Foundation at EdX.

In the real world, you are very likely to access Linux servers remotely using ssh. Who runs GUI on Linux servers? Unless you are working a cool company you are stuck with Windows on the laptop and almost everyone around you are likely to use PuTTy ssh client. I use Mobaxterm for many reasons one being  multi tabs support.

Let me guess further. It will be a virtual machine running on VMware ESXi. If your company has embraced public cloud, it will be an Amazon EC2 or Rackspace. There is a good chance you will not deal with a lot Linux physical servers.

A bit of history and early experience- Before ssh came along, the standard way to access remote Unix server was telnet. As a student in NIIT (once a upon a time a famous computer training institution in India), I used telnet client in Windows NT workstation to access the lone Linux ( Red Hat 6) server in the center. It was terrible because the terminal emulation was so so bad. For example, if you hit backspace, some charactes will appear instead. I used ssh only after I started working, my second job in 2004.
