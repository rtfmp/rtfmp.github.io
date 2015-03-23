---
layout: post
title: Where do commands in Linux come from?
---
When I started learning Linux, I knew there were two types of command-  shell builtin and external executables. Shell builtin are built into the shell and executables are binaries that are independent of the shell. But I never really thought for long time where do the commands come from. 

Some commands come from their own package whereas in some cases a single package would pack a bunch of commands. First to find out the type of command, I would use the `type` command. 

    [rtfmp@centos7 ~]$ type cd
    cd is a shell builtin
    [rtfmp@centos7 ~]$ type find
    find is /usr/bin/find
    [rtfmp@centos7 ~]$

If it is a shell builtin it tells me so. If it is not, it tells me the path where the command is stored. I could also use `which`, `whereis` and `locate` to find the path of a command. My preferred way is `which`.

    [rtfmp@centos7 ~]$ which find
    /usr/bin/find
    [rtfmp@centos7 ~]$ whereis find
    find: /usr/bin/find /usr/share/man/man1/find.1.gz
    [rtfmp@centos7 ~]$ locate find
    /usr/bin/find
    /usr/bin/findmnt
    /usr/bin/oldfind
    /usr/lib64/python2.7/modulefinder.py
    ...

Now I know where the command is stored, how do I find from which package it comes from? Use rpm -qf in CentOS 

    [rtfmp@centos7 ~]$ rpm -qf `which find`
    findutils-4.5.11-3.el7.x86_64

and dpkg -L in Ubuntu.

    brm@bacer:~$ dpkg -S `which find`
    findutils: /usr/bin/find

