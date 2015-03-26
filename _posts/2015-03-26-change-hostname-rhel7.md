---
layout: post
title: Changing hostname in RHEL 7
published: true
---
We are going to see the different ways of displaying and setting the hostname in RHEL 7. We will use commands like hostname, nmtui, nmcli and hostnamectl. Except for hostname command, all other methods survive a reboot.

##Using the hostname command
Display the hostname

    [rtfmp@centos7 ~]$ hostname
    centos7.localdomain

Change the hostname

    [rtfmp@centos7 ~]$ sudo hostname aizawl
    [sudo] password for rtfmp:

`hostname` will now display the new hostname

    [rtfmp@centos7 ~]$ hostname
    aizawl

but it will be reflected in the new prompt only for new sessions. To demonstrate I `su -` to root and ssh to my localhost and notice the hostname display in the prompt.

    [rtfmp@centos7 ~]$ sudo su -
    Last login: Wed Mar 25 16:21:51 IST 2015 on pts/0
    [root@aizawl ~]#
    [rtfmp@centos7 ~]$ ssh 0
    rtfmp@0's password:
    Last login: Wed Mar 25 16:27:57 2015 from localhost
    [rtfmp@aizawl ~]$

This will not survive a reboot as the change is not written to the configuration file. For RHEL 7 the hostname is stored in /etc/hostname.

    [rtfmp@aizawl ~]$ cat /etc/hostname
    centos7.localdomain
    [rtfmp@aizawl ~]$


##Editing  /etc/hostname

RHEL 7 has three hostnames. One of them, the static hostname is stored in  `/etc/hostname` and this is what we normally refer to as "the hostname". 

    [rtfmp@centos7 ~]$ cat /etc/hostname
    centos7.localdomain
    [rtfmp@centos7 ~]$

Yes, the `/etc/sysconfig/network` file is not in use anymore.

    [rtfmp@centos7 ~]$ cat /etc/sysconfig/network
    # Created by anaconda
    [rtfmp@centos7 ~]$

Lets change the hostname by editing `/etc/hostname`

    [rtfmp@centos7 ~]$ sudo vi /etc/hostname
    [rtfmp@centos7 ~]$ cat /etc/hostname
    aizawl.localdomain
    [rtfmp@centos7 ~]$

As with the hostname command, it is not immediately reflected in your prompt but this will survive a reboot.

    [rtfmp@centos7 ~]$ sudo su -
    Last login: Wed Mar 25 16:28:23 IST 2015 on pts/0
    [root@aizawl ~]#

    [rtfmp@centos7 ~]$ ssh 0
    rtfmp@0's password:
    Last login: Wed Mar 25 16:28:45 2015 from localhost
    [rtfmp@aizawl ~]$
    

In RHEL 7 comes a new command hostnamectl from Systemd. hostnametcl will not notice the change in `/etc/hostname` until reboot. To force hostnamectl to notice the change, restart hostnamed.

    [rtfmp@centos7 ~]$ hostnamectl
       Static hostname: centos7.localdomain
    Transient hostname: aizawl
             Icon name: computer
               Chassis: n/a
            Machine ID: fdba7161fada4540b992b6ce2065f909
               Boot ID: c2f2bf17217647fab772fe4edea88b5e
      Operating System: CentOS Linux 7 (Core)
           CPE OS Name: cpe:/o:centos:centos:7
                Kernel: Linux 3.10.0-123.20.1.el7.x86_64
          Architecture: x86_64


    [rtfmp@centos7 ~]$ sudo systemctl restart systemd-hostnamed
    [rtfmp@centos7 ~]$ hostnamectl
       Static hostname: aizawl.localdomain
             Icon name: computer
               Chassis: n/a
            Machine ID: fdba7161fada4540b992b6ce2065f909
               Boot ID: c2f2bf17217647fab772fe4edea88b5e
      Operating System: CentOS Linux 7 (Core)
           CPE OS Name: cpe:/o:centos:centos:7
                Kernel: Linux 3.10.0-123.20.1.el7.x86_64
          Architecture: x86_64
    [rtfmp@centos7 ~]$

##Using hostnamectl command

To displaying the hostname, run `hostnamectl` or `hostnamectl status`

    [rtfmp@aizawl ~]$ hostnamectl
       Static hostname: aizawl.localdomain
             Icon name: computer
               Chassis: n/a
            Machine ID: fdba7161fada4540b992b6ce2065f909
               Boot ID: 40ab23eece1540f98877df3837595f20
      Operating System: CentOS Linux 7 (Core)
           CPE OS Name: cpe:/o:centos:centos:7
                Kernel: Linux 3.10.0-123.20.1.el7.x86_64
          Architecture: x86_64


    [rtfmp@aizawl ~]$ hostnamectl status
       Static hostname: aizawl.localdomain
             Icon name: computer
               Chassis: n/a
            Machine ID: fdba7161fada4540b992b6ce2065f909
               Boot ID: 40ab23eece1540f98877df3837595f20
      Operating System: CentOS Linux 7 (Core)
           CPE OS Name: cpe:/o:centos:centos:7
                Kernel: Linux 3.10.0-123.20.1.el7.x86_64
          Architecture: x86_64

`hostnamectl` is a systemd command

    [rtfmp@aizawl ~]$ rpm -qf `which hostnamectl`
    systemd-208-11.el7_0.6.x86_64
    [rtfmp@aizawl ~]$

Now let us change the hostname 

    [rtfmp@aizawl ~]$ sudo hostnamectl set-hostname centos7


It is immmediately reflected in `hostname`, `/etc/hostname` and `hostnamectl` output

    [rtfmp@aizawl ~]$ hostname
    centos7
    [rtfmp@aizawl ~]$ cat /etc/hostname
    centos7
    [rtfmp@aizawl ~]$ hostnamectl
       Static hostname: centos7
             Icon name: computer
               Chassis: n/a
            Machine ID: fdba7161fada4540b992b6ce2065f909
               Boot ID: 40ab23eece1540f98877df3837595f20
      Operating System: CentOS Linux 7 (Core)
           CPE OS Name: cpe:/o:centos:centos:7
                Kernel: Linux 3.10.0-123.20.1.el7.x86_64
          Architecture: x86_64
    [rtfmp@aizawl ~]$

##Using nmcli
Displaying the hostname

    [rtfmp@aizawl ~]$ nmcli general hostname
    centos7

Change the hostname

    [rtfmp@aizawl ~]$ sudo nmcli general hostname aizawl
    [sudo] password for rtfmp:

Force hostnamectl to notice the change

    [rtfmp@aizawl ~]$ sudo systemctl restart systemd-hostnamed

Check the change is succcessfull
[rtfmp@aizawl ~]$ nmcli general hostname
aizawl
[rtfmp@aizawl ~]$

##Using nmtui

nmtui is a text based graphical tool. Simply run `nmtui` from the terminal as root or via sudo and choose `set system hostname`. Next restart hostnamed.

    [rtfmp@aizawl ~]$ sudo systemctl restart systemd-hostnamed
