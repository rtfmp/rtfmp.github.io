---
layout: post
title: How do I scan UDP port?
published:false
---
Off late, a couple of friends are asking me how I check if I can connect to a port on a remote host. They have  tried telnet-ing to the port but it does not work because they are UDP ports. After a bit of googling and reading the man page, this is how I scan UDP ports using nc and nmap.

Using nc:
    nc -uz remote-host udp-port

Sample out:

If the remote host is not accepting the connection, it instantly returns the prompt without any message or takes a while sometimes.

Using nmap:
    nmap -p U:53 192.168.1.1

nmap -sU  remote-host
nmap -sU -p514  remote-host

