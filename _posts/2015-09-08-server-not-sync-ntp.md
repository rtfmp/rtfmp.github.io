---
layout: post
title: No matter what I do, my server can't synchronize with NTP
comments: yes
---
So I have a RHEL 6 server configured with  the correct NTP server. Correct? At least that is I what thought. But it will not sync   with the NTP server. I can ping the NTP server, I can connect  to it using nc to UDP port 123. I have restarted  ntpd a couple of times and it still won't work. What the hell is wrong?

Ping ntp server? Yes.
nc -vzu ntp.example.com 123? yes

    nc -vzu ntp.example.com 123
    Connection to ntp.example.com 123 port [udp/ntp] succeeded!

Tried force sync using ntpdate? Checked

    ntpdate -d  ntp.example.com
    20 Aug 20:56:14 ntpdate[11025]: ntpdate 4.2.6p5@1.2349-o Mon Mar  9 14:24:09 UTC 2015 (1)
    Looking for host ntp.example.com and service ntp
    host found : ns1.cat.com
     transmit(192.168.1.50)
    transmit(192.168.1.50)
    transmit(192.168.1.50)
    transmit(192.168.1.50)
    transmit(192.168.1.50)
    192.168.1.50: Server dropped: no data
    server 192.168.1.50, port 123
    stratum 0, precision 0, leap 00, trust 000
    refid [192.168.1.50], delay 0.00000, dispersion 64.00000
    transmitted 4, in filter 4
    reference time:    00000000.00000000  Mon, Jan  1 1900  6:55:25.000
    originate timestamp: 00000000.00000000  Mon, Jan  1 1900  6:55:25.000
    transmit timestamp:  d9804cf2.19367c4f  Thu, Aug 20 2015 20:56:18.098
    filter delay:  0.00000  0.00000  0.00000  0.00000
         0.00000  0.00000  0.00000  0.00000
    filter offset: 0.000000 0.000000 0.000000 0.000000
         0.000000 0.000000 0.000000 0.000000
    delay 0.00000, dispersion 64.00000
    offset 0.000000

    20 Aug 20:56:19 ntpdate[11025]: no server suitable for synchronization found

The answer to the problem lies in this part of the output

    transmit(192.168.1.50)
    transmit(192.168.1.50)
    transmit(192.168.1.50)
    transmit(192.168.1.50)
    transmit(192.168.1.50)
    192.168.1.50: Server dropped: no data

I found a [Red Hat KB article](https://access.redhat.com/solutions/66485) which says "Server dropped: no data" indicates a problem.  Besides, my server is transmitting- "transmit(192.168.1.50" but it is not receiving. On a working server, running the same command, this is what I get. Note that it is sending and receiving.


    ntpdate -d ntp1.corp.cat.com
    4 Sep 01:08:52 ntpdate[23977]: ntpdate 4.2.6p5@1.2349-o Mon Mar  9 14:24:09 UTC 2015 (1)
    Looking for host ntp1.corp.cat.com and service ntp
    host found : ntp.corp.cat.com
    transmit(192.168.1.50)
    receive(192.168.1.50)
    transmit(192.168.1.50)
    receive(192.168.1.50)
    transmit(192.168.1.50)
    receive(192.168.1.50)
    transmit(192.168.1.50)
    receive(192.168.1.50)
    server 192.168.1.50, port 123
    stratum 4, precision -24, leap 00, trust 000
    refid [192.168.1.50], delay 0.02588, dispersion 0.00000
    transmitted 4, in filter 4
    reference time:    d993b3c7.b3909a66  Fri, Sep  4 2015  1:08:07.701
    originate timestamp: d993b3f5.0af26a1f  Fri, Sep  4 2015  1:08:53.042
    transmit timestamp:  d993b3f5.0ac72c86  Fri, Sep  4 2015  1:08:53.042
    filter delay:  0.02592  0.02600  0.02591  0.02588
         0.00000  0.00000  0.00000  0.00000
    filter offset: 0.000535 0.000465 0.000516 0.000521
         0.000000 0.000000 0.000000 0.000000
    delay 0.02588, dispersion 0.00000
    offset 0.000521

    4 Sep 01:08:53 ntpdate[23977]: adjust time server 192.168.1.50 offset 0.000521 sec


After talking to team managing NTP, the server is configured  with the wrong NTP server. After replacing it with the right NTP server, I am back in business.
