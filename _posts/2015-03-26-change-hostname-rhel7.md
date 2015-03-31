---
layout: post
title: Configure hostname in RHEL 7
published: true
---
- [Using the hostname command](#hostname)
- [Editing  /etc/hostname](#etc_hostname)
- [Using hostnamectl command](#hostnamectl)
- [Using nmcli](#nmcli)
- [Using nmtui](#nmtui)

We are going to see the different ways of displaying and setting hostname in RHEL 7. We will use commands like hostname, nmtui, nmcli, hostnamectl. Except for hostname command, all other methods will survive a reboot. To make hostnamectl aware of the change by other commands, we will restart hostnamed. hostnamectl is a part of systemd.  

##Using the hostname command<a id="hostname"></a>
Display the hostname

    [rtfmp@centos7 ~]$ hostname

Change the hostname

    [rtfmp@centos7 ~]$ sudo hostname newhostname

Verify

    [rtfmp@centos7 ~]$ hostname
    
Setting hostname with `hostname` is effective only until reboot.

##Editing  /etc/hostname<a id="etc_hostname"></a>

RHEL 7 has three hostnames. One of them, the static hostname is stored in  `/etc/hostname` and this is what we normally refer to as "the hostname". 

Display the hostname

    [rtfmp@centos7 ~]$ cat /etc/hostname

Yes, the `/etc/sysconfig/network` file is not in use anymore.

Change the hostname

    [rtfmp@centos7 ~]$ sudo vi  /etc/hostname
    [rtfmp@centos7 ~]$ sudo systemctl restart systemd-hostnamed
    
Remember to enter the FQDN.

Verify

    [rtfmp@centos7 ~]$ cat /etc/hostname
    
##Using hostnamectl command<a id="hostnamectl"></a>

To display the hostname, run `hostnamectl` or `hostnamectl status`

    [rtfmp@centos7 ~]$ hostnamectl
       Static hostname: centos7.localdomain
             Icon name: computer
               Chassis: n/a
            Machine ID: fdba7161fada4540b992b6ce2065f909
               Boot ID: 40ab23eece1540f98877df3837595f20
      Operating System: CentOS Linux 7 (Core)
           CPE OS Name: cpe:/o:centos:centos:7
                Kernel: Linux 3.10.0-123.20.1.el7.x86_64
          Architecture: x86_64


Change the hostname 

    [rtfmp@centos7 ~]$ sudo hostnamectl set-hostname newhostname

Verify

    [rtfmp@centos7 ~]$ hostname

##Using nmcli<a id="nmcli"></a>
Displaying the hostname

    [rtfmp@centos7 ~]$ nmcli general hostname

Change the hostname

    [rtfmp@centos7 ~]$ sudo nmcli general hostname newhostname
    [rtfmp@centos7 ~]$ sudo systemctl restart systemd-hostnamed

Verify

    [rtfmp@centos7 ~]$ hostname

##Using nmtui<a id="nmtui"></a>
nmtui is a text based graphical tool. Simply run `nmtui` from the terminal as root or via sudo and choose `set system hostname` and restart hostnamed.

    [rtfmp@centos7 ~]$ nmtui
    [rtfmp@centos7 ~]$ sudo systemctl restart systemd-hostnamed
