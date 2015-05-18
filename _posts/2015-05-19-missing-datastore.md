---
layout: post
title: Dude, where's my VMFS datastore?
---
Consider these situations. Suddenly, a VM goes down and the datastore on which it sits is greyed out in vCenter. A virtual machine is unable access one of it's disks. You are unable to browse the datastore because it is greyed out. The VMFS datastore is inaccessible across hosts in the cluster.

What can we do? The first thing we need to check is, can the host access the VMFS datastore? Most likely not because we can't browser it from vSphere client GUI. To be certain, we can check it from the command line. ssh to the ESXi host and run one of these commands

    esxcfg-scsidevs -m | grep -i datastore_name
    esxcfg-info  -s   | grep -i datastore_name

If there is no output, the datastore isn't there. The next question is, does the host see the SAN LUN? Run one of these commands.

    esxcfg-scsidevs -l | grep -i naa.ID
    esxcli storage core device list | grep -i  naa.ID
    esxcfg-mpath  -l | grep -i naa.ID
    esxcli storage nmp device list | grep -i  naa.ID

If it is important to have grep ignore case because the SAN guy gave NAA ID with upper case letters whereas ESXi detects them with lower case. I lost close to an hour because I was unaware of this.

If you don't see the LUN, we need to talk to the SAN admin. But if we do see the LUN, what happens to the datastore? Maybe there isn't a valid VMFS partition on the LUN. To check that run

    fdisk -l /vmfs/devices/disks/naa.ID or 
    partedUtil  getptbl /vmfs/devices/disks/naa.ID

This awesome [script](http://kb.vmware.com/kb/2046610) from VMware will print out the partition information for SAN LUNs.

```
offset="128 2048"; for dev in `esxcfg-scsidevs -l | grep "Console Device:" | awk {'print $3'}`; do disk=$dev; echo $disk; partedUtil getptbl $disk; { for i in `echo $offset`; do echo "Checking offset found at $i:"; hexdump -n4 -s $((0x100000+(512*$i))) $disk; hexdump -n4 -s $((0x1300000+(512*$i))) $disk; hexdump -C -n 128 -s $((0x130001d + (512*$i))) $disk; done; } | grep -B 1 -A 5 d00d; echo "---------------------"; done
```

Example of a LUN with valid VMFS partition

    /vmfs/devices/disks/naa.6005076801810209f000000000000089
    msdos
    130541 255 63 2097152000
    1 128 2097141164 251 0
    Checking offset found at 128:
    0110000 d00d c001
    0110004
    1310000 f15e 2fab
    1310004
    0131001d  53 4d 54 49 4e 54 47 58  52 5f 36 34 00 00 00 00  |MY-DATASTORE....|
    0131002d  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    ---------------------

Example of a LUN without valid VMFS partition

    /vmfs/devices/disks/naa.6005076801810209f000000000000025
    msdos
    130541 255 63 2097152000
    ---------------------

If the LUN does not have VMFS partition, good luck recreating the partition. Read VMware KB article: [ESXi 5.x](http://kb.vmware.com/kb/2046610), below [ESXi 5.x](http://kb.vmware.com/kb/1002281)
