- [Set hostname only](#sethostname)
- [Set domain](#setdomain)
- [Set Fully Qualified Domain Name in one shot](#setfqdn)
- [Display hostnamem using esxcli command](#esxclihostname)

##Set hostname only<a id="sethostname"></a>
    ~ # esxcli system hostname set   --host farstar
    ~ # hostname
    farstar.localdomain
    ~ #
    OR
    ~ # esxcli system hostname set -H farstar
    ~ # hostname
    farstar.localdomain
    ~ #

##Set domain<a id="setdomain"></a>
    ~ # esxcli system hostname set   --domain farstar.rtfmp.com
    ~ # hostname
    farstar.farstar.rtfmp.com
    ~ #
    OR
    ~ # esxcli system hostname set -d rtfmp.com
    ~ # hostname
    farstar.rtfmp.com
    ~ #

##Set Fully Qualified Domain Name in one shot<a id="setfqdn"></a>
    ~ # esxcli system hostname set --fqdn farstar.rtfmp.com
    ~ # hostname
    farstar.rtfmp.com
    ~ #
    OR
    ~ # esxcli system hostname set -f farstar.rtfmp.com

##Display hostname using esxcli<a id="esxclihostname"></a>
    ~ # esxcli system hostname get
       Domain Name: rtfmp.com
       Fully Qualified Domain Name: farstar.rtfmp.com
       Host Name: farstar
    ~ #



