- [List DNS servers](#list)
- [Add DNS server](#add)
- [Remove DNS server](#remove)
- [Add DNS search list](#search)
 

##List DNS servers<a id="list"></a>
    ~ # esxcli network ip dns server list
       DNSServers: 192.168.76.2
    ~ #


##Add DNS server<a id="add"></a>
    ~ # esxcli network ip  dns server add -s 8.8.8.8
    ~ # esxcli network ip dns server list
       DNSServers: 192.168.76.2, 8.8.8.8
    ~ #

##Remove DNS server<a id="remove"></a>
    ~ # esxcli network ip  dns server remove -s 8.8.8.8
    ~ # esxcli network ip dns server list
       DNSServers: 192.168.76.2
    ~ #

##Add DNS search list<a id="search"></a>
    ~ # esxcli network ip dns search add -d intra.rtfmp.com
    ~ # esxcli network ip dns search list
       DNSSearch Domains: localdomain, intra.rtfmp.com
    ~ #
