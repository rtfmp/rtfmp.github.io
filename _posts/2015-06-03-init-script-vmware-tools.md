---
layout: post
title: Create init script for VMware tools
---
I installed RHEL 6 on VMware ESXi 5.1 and after installing VMware tools, the installer didnot add an init script for VMware tools. Which means  `service vmware-tools whatever` will return unrecognized service. VMware has a [KB article](kb.vmware.com/kb/2015685) which tells us to use their own script but does not tell us how to create an init script.

Thankfully, this gentleman has the [recipe](http://klovsjo.free.fr/?p=70) and if you need to repeat it, here's a quick script.

    # Create init script for VMware tools if it does not exist
    # http://klovsjo.free.fr/?p=70
    
    if [[ ! -e /etc/init.d/vmware-tools ]]
    then
            cp /etc/vmware-tools/services.sh /etc/init.d/vmware-tools
            sed -i 's/##VMWARE_INIT_INFO##/# Basic support for IRIX style chkconfig\n# chkconfig: 235 03 99\n# description: Manages the services needed to run VMware software\n/' /etc/init.d/vmware-tools
            chkconfig --add vmware-tools
            chkconfig vmware-tools on
    fi

