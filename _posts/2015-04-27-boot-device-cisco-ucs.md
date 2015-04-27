---
layout: post
title: Creating boot device on a Cisco UCS C Series rack mount server
---
I am going to install ESXi on a bunch of UCS C Series rack mount servers. They are M4's with SD card (from Cisco called FlexFlash) and I want to install ESXi on the SD card. I want to create boot devices and order such that KVM mapped ISO is the first boot device followed by the SD card. I can do this from the CIMC GUI but there's always a faster way. I ssh to the CIMC as admin and paste these commands into the terminal.

    scope bios
    create-boot-device Hypervisor SDCARD
    create-boot-device DVD VMEDIA
    commit
    scope boot-device DVD
    set subtype KVMDVD
    set order 1
    set state Enabled
    commit
    exit
    scope boot-device Hypervisor
    set order 2 
    set state Enabled
    commit
    exit

The CIMC commandline is a no brainer if you have the love for command line and a minimal experience on IOS.  To do anything BIOS related- show or set, you enter the BIOS scope which I did in the first line using the command `scope bios`. I create the `create-boot-device` to create boot devices. Next, I entered into each device to do further settings. And to make change persistent, I run the `commit` command. 

Here I  display the  boot order settings that were created

    C220-XXXXXXXXXXX /bios # show boot-device
    Boot Device                    Device Type  Device State       Device Order
    ------------------------------ ------------ ------------------ ----------------
    DVD                            VMEDIA       Enabled            1
    Hypervisor                     SDCARD       Enabled            2
    C220-xxxxxxxxxxx /bios #
    

