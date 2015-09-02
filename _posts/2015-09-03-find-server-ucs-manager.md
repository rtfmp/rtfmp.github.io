---
layout: post
title: Check if a server is available on an UCS Manager using the CLI
comment: true
---
I have the name of an UCS B series blade server that I need to configure. I need to locate the blade on an UCS Manager.

We can login to the UCS Manager GUI but that will take a long time. We can do it much quicker on the CLI.  Ssh to the UCSM and and run these commands

    ssh user@ucsm
    UCSM-A# show service-profile  status | grep server_name

This will work if the service-profile matches the server. But the service profile may be named differently ( which I hate) than the server name.  Try this

    UCSM-A /org # scope org GIS_HADOOP
    UCSM-A /org # show  service-profile detail | grep 'User Label:' | grep server_name

Hopefully, the user label should have the server name. The user label is what you see within bracket next to profile name in the UCSM GUI. 
