---
layout: post
title: How do I scan UDP ports?
---
Off late, a couple of friends are asking me how to check if we can connect to a port on a remote host. They have  tried telnet-ing to the port but it does not work because they are UDP ports. After a bit of googling and reading the man page, this is how we can scan UDP ports using nc and nmap.

Using nc:

      nc -uz remote-host udp-port

Sample output:

      [root@server1 ~]# nc -z -u remote_host 514
      Connection to remote_host 514 port [udp/syslog] succeeded!
      [root@server1 ~]#

If the remote host is not accepting the connection, it instantly returns the prompt without any message or takes a while sometimes.

Using nmap:
    nmap -sU -pPortNumer  remote-host

Sample output:

      [root@server1 ~]# nmap -sU -p514 remote_host
            
      Starting Nmap 4.11 ( http://www.insecure.org/nmap/ ) at 2015-05-11 09:00 IST
      Interesting ports on remote_host (IP_ADDRESS):
      PORT    STATE         SERVICE
      514/udp open|filtered syslog
            
      Nmap finished: 1 IP address (1 host up) scanned in 0.398 seconds

