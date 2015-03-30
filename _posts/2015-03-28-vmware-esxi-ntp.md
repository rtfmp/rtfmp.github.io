---
layout: post
title: Configure NTP in ESXi from the command line
---

- [Display NTP settings](#catntp.conf)
- [Configure NTP server](#vintp.conf)
- [Restart NTP](#ntpdrestart)
- [Enable NTP Service](#chkconfigntp)
- [Display NTP peers](#ntpq-p)
- [Replace NTP server](#replacentpdserver)
- [Remove NTP server](#removentp)
- [Remove blank lines in ntp.conf](#removeblank)
- [Replace ntp.conf](#sedntpserver)
- [Run the commands over ssh](#runssh)
- [Repeat the commands for multiple hosts over ssh](#runmult)

## Display NTP settings<a id="catntp.conf"></a>
    ~ # cat /etc/ntp.conf
    restrict 127.0.0.1
    restrict default kod nomodify notrap
    driftfile /etc/ntp.drift
    ~ #
This is a freshly installed server and there's no ntp server configured.

## Configure NTP server<a id="vintp.conf"></a>
We will use NTP Pool Project servers.

        ~ # vi /etc/ntp.conf
        ~ # cat /etc/ntp.conf
        restrict 127.0.0.1
        restrict default kod nomodify notrap
        server 0.pool.ntp.org
        server 1.pool.ntp.org
        driftfile /etc/ntp.drift
        ~ #

## Restart NTP<a id="ntpdrestart"></a>
    ~ # /etc/init.d/ntpd restart
    Stopping ntpd
    Starting ntpd
    ~ #

## Display NTP peers<a id="ntpq-p"></a>
    ~ # ntpq -p
         remote           refid      st t when poll reach   delay   offset  jitter
    ==============================================================================
     ns02.hns.net.in .INIT.          16 u    -   64    0    0.000    0.000   0.000
     123.108.200.124 .INIT.          16 u    -   64    0    0.000    0.000   0.000
    ~ #

It could take sometime to sync.

        ~ # ntpq -p
             remote           refid      st t when poll reach   delay   offset  jitter
        ==============================================================================
        +123.108.200.124 103.252.195.20   3 u   46   64  377   81.459    4.968   3.615
        *web10.hnshostin 158.43.128.33    2 u   44   64  377   38.879    5.120   5.157
        ~ #


##Enable NTP service<a id="chkconfigntp"></a>

        ~ # chkconfig ntpd on
        ~ # chkconfig --list | grep ntpd
        ntpd                            on
        ~ #


## Replace NTP server<a id="replacentpdserver"></a>
    sed -i 's/ntpserver/newntpserver/' /etc/ntp.conf

## Remove NTP server<a id="removentp"></a>
    sed -i 's/ntpserver//' /etc/ntp.conf

## Remove blank lines in ntp.conf<a id="removeblank"></a>
    ~ # sed -i  '/^\s*$/d' /etc/ntp.conf

## Replace ntp.conf with new settings<a id="sedntpserver"></a>
    ~ # cat <<EOF > /etc/ntp.conf
    > restrict 127.0.0.1
    > restrict default kod nomodify notrap
    > server 0.pool.ntp.org
    > server 1.pool.ntp.org
    > driftfile /etc/ntp.drift
    > EOF
    ~ # cat /etc/ntp.conf
    restrict 127.0.0.1
    restrict default kod nomodify notrap
    server 0.pool.ntp.org
    server 1.pool.ntp.org
    driftfile /etc/ntp.drift
    ~ #


## Run the commands over ssh<a id="runssh"></a>
        ssh root@servername command_to_run
    
## Repeat the commands for multiple hosts over ssh <a id="runmult"></a>
        cat > server_list
        Paste the list of servers
        Ctrl+D
        
        for i in `cat server_list`
        do
        ssh root@$i  command_to_run
        done

