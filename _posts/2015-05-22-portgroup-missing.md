---
layout: post
title: ESXi portgroup missing and here's the proof
---
The backup guys came over because a few Avamar Agent backup stopped working for a few virtual machines. Avamar Agent backup uses a dedicated network and hence the VMs have a second interface for the agent. We checked the interface is up with `ifconfig eth1` and  `ethtool eth1` shows it has a link. However, `tcpdump -i eth1` detects zero traffic. What are we missing?

The interface for avamar in each VM is connected to portgroup- Backup-Network- which we find is no longer existing. If you look up the VM in vCenter, the portgroup would be greyed out.  We ssh to the host and `esxcfg-vswitch -l` shows there's no such portgroup. The issue is resolved by creating the portgroup and reconnecting the VM to the portgroup. In vCenter

- Right click on the VM
- Edit Settings
- Click on the Adapter, you will notice the portgroup is blank
- Choose the portgroup

This step is necessary. Recreating the portgroup alone is not enough for the VM to reconnect to what was existing portgroup. I think this could be due to internal reference VMware uses for a portgroup.

What happened to the portgroup? Well, it was removed. Greping through hostd log, we found this

    for i in /scratch/log/hostd.?.gz
    do
    zcat $i | grep -C5  Erase
    done
    
    2015-05-20T10:25:52.847Z [594D7B90 verbose 'Hostsvc.NetworkProvider' opID=131DAF7A-0000094A-aa-61] Erase pgName: Backup-Network

Looking a few lines up and down this message, we find the start and stop of the event

    2015-05-20T10:25:52.735Z [59741B90 info 'Vimsvc.TaskManager' opID=131DAF7A-0000094A-aa-61] Task Created : haTask-ha-host-vim.host.NetworkSystem.invokeHostTransactionCall-322150621
    2015-05-20T10:25:52.748Z [594D7B90 verbose 'Hostsvc.NetworkProvider' opID=131DAF7A-0000094A-aa-61] ActionSetVirtualSwitchPortGroup: Updating portgroup key-vim.host.PortGroup-Backup-Network
    2015-05-20T10:25:52.847Z [594D7B90 verbose 'Hostsvc.NetworkProvider' opID=131DAF7A-0000094A-aa-61] Erase pgName: Backup-Network
    2015-05-20T10:25:52.848Z [594D7B90 verbose 'Hostsvc' opID=131DAF7A-0000094A-aa-61] Invoking transaction 161 method updatePortGroup
    2015-05-20T10:25:52.848Z [594D7B90 info 'Vimsvc.TaskManager' opID=131DAF7A-0000094A-aa-61] Task Completed : haTask-ha-host-vim.host.NetworkSystem.invokeHostTransactionCall-322150621 Status success

So who did it? In vCenter

- Click on the host
- Task
- Look for an Update task at the same timestamp (note hostd log is in UTC)
- You will find your man there
