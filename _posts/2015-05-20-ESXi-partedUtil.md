---
title: ESXi partedUtil for when trouble strikes
layout: post
---
A VMware admin would never have to worry about using partedUtil to manipulate partition table on the CLI until trouble strikes.Starting VMFS5 (ESX 5.x and above), VMware switched to GUID Partition Table (gpt) from MBR partition(labled msdos) to support bigger extent that larger than 2TB. fdisk does not support gpt partition and hence partedUtil. 

partedUtil without any arguments will  display the options available and syntax.

    ~ # partedUtil
    Not enough arguments
    
    Usage:
     Get Partitions : get <diskName>
     Set Partitions : set <diskName> ["partNum startSector endSector type attr"]*
     Delete Partition : delete <diskName> <partNum>
     Resize Partition : resize <diskName> <partNum> <start> <end>
     Get Partitions : getptbl <diskName>
     Set Partitions : setptbl <diskName> <label> ["partNum startSector endSector type/guid attr"]*
     Fix Partition Table : fix <diskName>
     Create New Label (all existing data will be lost): mklabel <diskName> <label>
     Show commonly used partition type guids : showGuids
     Get usable first and last sectors : getUsableSectors <diskName>
     Fix GPT Table interactively : fixGpt <diskName>

To display the partition information on a disk, we use the `getptbl` option

    partedUtil  getptbl  <path to device>

This is  native VMFS5 partition

    ~ # partedUtil getptbl /dev/disks/naa.6005076801810209f000000000000273
    gpt
    20233 255 63 325058560
    1 2048 325058526 AA31E02A400F11DB9590000C2911D1B8 vmfs 0

But this is a VMFS3 partition converted to VMFS5 still using MBR

    ~ # partedUtil getptbl /vmfs/devices/disks/naa.6005076801810209f000000000000042
    msdos
    65270 255 63 1048576000
    1 128 1048562549 251 0


And this is disk with no partition

    ~ # partedUtil getptbl /vmfs/devices/disks/mpx.vmhba1:C0:T1:L0
    unknown
    13054 255 63 209715200


Lets create a partition. VMFS5 partition starts at sector. The end sector is calculated as (Cylinders * Heads * Sectors Per track) -1. Therefore  (13054*255*63) -1 = 209712509 is our end sector.

      ~ # partedUtil setptbl   /vmfs/devices/disks/mpx.vmhba1:C0:T1:L0 gpt "1 2048 209712509 AA31E02A400F11DB9590000C2911D1B8 0"
      gpt
      0 0 0 0
      1 2048 209712509 AA31E02A400F11DB9590000C2911D1B8 0

Display  the partition table

    ~ # partedUtil getptbl   /vmfs/devices/disks/mpx.vmhba1:C0:T1:L0
    gpt
    13054 255 63 209715200
    1 2048 209712509 AA31E02A400F11DB9590000C2911D1B8 vmfs 0
    ~ #


We can now create a VMFS filesystem using vmkfstools

    ~ # vmkfstools --createfs vmfs5 --blocksize 1m -S MyVol  /vmfs/devices/disks/mpx.vmhba1:C0:T1:L0:1
    create fs deviceName:'/vmfs/devices/disks/mpx.vmhba1:C0:T1:L0:1', fsShortName:'vmfs5', fsName:'MyVol'
    deviceFullPath:/dev/disks/mpx.vmhba1:C0:T1:L0:1 deviceFile:mpx.vmhba1:C0:T1:L0:1
    Checking if remote hosts are using this device as a valid file system. This may take a few seconds...
    Creating vmfs5 file system on "mpx.vmhba1:C0:T1:L0:1" with blockSize 1048576 and volume label "MyVol".
    Successfully created new volume: 555acc28-b82a2519-a19d-000c29b96d8d
    ~ #

Checking  our datatore is available

    ~ # esxcfg-scsidevs -m
    mpx.vmhba1:C0:T0:L0:3                                            /vmfs/devices/disks/mpx.vmhba1:C0:T0:L0:3 551af5af-ec996c3b-1447-000c29b96d8d  0  datastore1
    mpx.vmhba1:C0:T1:L0:1                                            /vmfs/devices/disks/mpx.vmhba1:C0:T1:L0:1 555acc28-b82a2519-a19d-000c29b96d8d  0  MyVol
    ~ #


    ~ # df -h
    Filesystem   Size   Used Available Use% Mounted on
    VMFS-5      24.5G 940.0M     23.6G   4% /vmfs/volumes/datastore1
    VMFS-5      99.8G 972.0M     98.8G   1% /vmfs/volumes/MyVol
    vfat         4.0G   5.6M      4.0G   0% /vmfs/volumes/551af5b0-29038311-f8ab-000c29b96d8d
    vfat       249.7M 169.8M     80.0M  68% /vmfs/volumes/50d0cb7f-d62971a8-9999-37ee6ba7cd25
    vfat       249.7M   8.0K    249.7M   0% /vmfs/volumes/3b070c72-5e2d3ec8-9dad-140ca4b003f3
    vfat       285.8M 193.4M     92.4M  68% /vmfs/volumes/551af5a9-0a27368c-5550-000c29b96d8d
    ~ #


Delete partition

    ~ # partedUtil delete /vmfs/devices/disks/mpx.vmhba1:C0:T1:L0 1

The partition is gone

    ~ # partedUtil getptbl   /vmfs/devices/disks/mpx.vmhba1:C0:T1:L0
    gpt
    13054 255 63 209715200
    ~ #

Lastly, display GUIDs  used by ESXi

    ~ # partedUtil showGuids
     Partition Type       GUID
     vmfs                 AA31E02A400F11DB9590000C2911D1B8
     vmkDiagnostic        9D27538040AD11DBBF97000C2911D1B8
     vsan                 381CFCCC728811E092EE000C2911D0B2
     VMware Reserved      9198EFFC31C011DB8F78000C2911D1B8
     Basic Data           EBD0A0A2B9E5443387C068B6B72699C7
     Linux Swap           0657FD6DA4AB43C484E50933C84B4F4F
     Linux Lvm            E6D6D379F50744C2A23C238F2A3DF928
     Linux Raid           A19D880F05FC4D3BA006743F0F84911E
     Efi System           C12A7328F81F11D2BA4B00A0C93EC93B
     Microsoft Reserved   E3C9E3160B5C4DB8817DF92DF00215AE
     Unused Entry         00000000000000000000000000000000


Recommended reading:

* [Using the partedUtil command line utility on ESXi and ESX (1036609)](kb.vmware.com/kb/1036609)
* [Using vmkfstools to Manage VMFS Datastores](http://buildvirtual.net/using-vmkfstools-to-manage-vmfs-datastores/)
* [How to Format and Create VMFS5 Volume using the CLI in ESXi 5](http://buildvirtual.net/using-vmkfstools-to-manage-vmfs-datastores/) 


