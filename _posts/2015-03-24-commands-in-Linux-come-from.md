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

Now I know where the command is stored, how do I find from which package it comes from? Use `rpm -qf` in CentOS 

    [rtfmp@centos7 ~]$ rpm -qf `which find`
    findutils-4.5.11-3.el7.x86_64

and `dpkg -S` in Ubuntu.

    brm@bacer:~$ dpkg -S `which find`
    findutils: /usr/bin/find

The Yum equivalent is `yum provides`.

        [rtfmp@centos7 ~]$ yum provides find
        Loaded plugins: fastestmirror
        Loading mirror speeds from cached hostfile
         * base: mirror.nbrc.ac.in
         * extras: mirror.digistar.vn
         * updates: mirror.nbrc.ac.in
        1:findutils-4.5.11-3.el7.x86_64 : The GNU versions of find utilities (find and
                                        : xargs)
        Repo        : base
        Matched from:
        Filename    : /usr/bin/find
        
        
        
        1:findutils-4.5.11-3.el7.x86_64 : The GNU versions of find utilities (find and
                                        : xargs)
        Repo        : @anaconda
        Matched from:
        Filename    : /usr/bin/find
        
        
        
        [rtfmp@centos7 ~]$

I haven't found an exact equivalent for apt-get but `apt-file search` is close.

Since we have found which package provides the find command which is findutils, lets check if this package has other commands or binaries.

        [rtfmp@centos7 ~]$ rpm -ql findutils | grep bin
        /usr/bin/find
        /usr/bin/oldfind
        /usr/bin/xargs
        [rtfmp@centos7 ~]$

The Debian\Ubuntu way would be

        brm@bacer:~$ dpkg -L findutils | grep bin
        /usr/bin
        /usr/bin/oldfind
        /usr/bin/find
        /usr/bin/xargs
        brm@bacer:~$

This tells `xargs` comes from the same package as find. I was not aware of this in spite of using Linux for a few good years.

Two packages that would be worth inspecting are `coreutils` and `util-linux` also called `util-linux-ng` in some version. A lot of commands that we use on a daily basis come from these two packages. The following output gives us a brief idea of how many binaries are packed with them.

        [rtfmp@centos7 ~]$ rpm -ql coreutils | grep bin | wc -l
        102
        [rtfmp@centos7 ~]$

        brm@bacer:~$ dpkg -L coreutils | grep bin | wc -l
        107
        brm@bacer:~$

I hope it's been interesting to you and thank you for reading.
