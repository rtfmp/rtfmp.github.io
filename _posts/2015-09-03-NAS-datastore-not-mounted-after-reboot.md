---
layout: post
title: NAS datastores are not mounted after rebooting ESXi 5.5
comments: true
---

After rebooting an ESXi 5.5 U2 host, NAS datastores are not mounted. I had to run "esxcfg-nas -r" to get them mounted. It is similar to this issue discussed in this [community forum thread](https://communities.vmware.com/message/2433715#2433715). The reason is ESXi tries to restore NAS mounts before networking is fully up and DNS resolution fails. The host uses DNS for mount NAS.

    grep -i nfs sys*.log
    sysboot.log:[13:12:44.964828] sysboot: nfs 72
    sysboot.log:[13:12:54.268838] sysboot: restore-nfs-volumes 89
    syslog.log:2015-08-25T13:13:04Z jumpstart: unhandled exception whilst processing restore-nfs-volumes: Unable to    resolve hostname
    'nas-server.example.com'

From the boot sequence, there's few seconds gaps between network and NAS mount restoration.

    /var/log # grep -iE 'nfs|network' sysboot.log
    [13:12:27.397596] sysboot: networking-drivers 35
    [13:12:31.994166] sysboot: network-uplink-init 49
    [13:12:32.026207] sysboot: network-support 52
    [13:12:44.845141] sysboot: restore-networking 66
    [13:12:44.913433] sysboot: auto-configure-networking 68
    [13:12:44.964828] sysboot: nfs 72
    [13:12: .268838] sysboot: restore-nfs-volumes 89

The fix is to use IP address instead of DNS name or add "esxcfg-nas -r" to /etc/rc.local.d/local.sh. VMware's [Best Practices for NAS](http://www.vmware.com/resources/techresources/10096) does not have a preference of IP over DNS but does acknowledge the additional depency on DNS.

It is possible you already have the NAS datastores mounted using DNS across the cluster. In which case changing it to IP would result in "(1)" being suffixed to the datastore name. This would result in vMotion failing etc.. Unless you can take all VMs down and re-register them, rescanning NAS on startup is an easier way out.
