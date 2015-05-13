---
layout: post
title: Which ESXi VMkernel interface is used for NAS?
comments: true
---
I have two VMkernel interfaces in my ESXi server and I use NAS datastores. I would like to know which of my vmk interfaces is used for NAS. Sure, I can ask the NAS Admin. Hey, here's a volume on this filer, can you give me the list of IPs it is exported to? But I am a Linux admin and I would like to run some commands and I find it myself.

List the VMKernel interfaces

    esxcfg-vmknic -l

Get the NAS filer IP

    esxcfg-nas -l

Then I run this command 

    tcpdump-uw -i vmk0 dst mynasfiler | grep nfs
    11:54:59.228779 IP truncated-ip - 8450 bytes missing! myesxihost.925 > mynasfiler.nfs: Flags [.], ack 40445, win 512,     options [nop,nop,TS val 557074583 ecr 557506100], length 8480
    11:54:59.228801 IP truncated-ip - 5762 bytes missing! myesxihost.925 > mynasfiler.nfs: Flags [.], ack 40445, win 512,     options [nop,nop,TS val 557074583 ecr 557506100], length 5792
    11:54:59.228951 IP truncated-ip - 11658 bytes missing! myesxihost.925 > mynasfiler.nfs: Flags [.], ack 40445, win 512     , options [nop,nop,TS val 557074583 ecr 557506100], length 11688
    11:54:59.229095 IP truncated-ip - 12794 bytes missing! myesxihost.925 > mynasfiler.nfs: Flags [.], ack 40445, win 512     , options [nop,nop,TS val 557074583 ecr 557506100], length 12824

This tells me there is NFS traffic between the ESXi host and NAS filer on the vmk0 VMkernel interface. 
