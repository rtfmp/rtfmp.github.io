- [List DNS servers](#list)
- [Add DNS server](#add)
- [Remove DNS server](#remove)
- [List DNS search list](#listsearch)
- [Add DNS search list](#addsearch)
- [Remove DNS search list](#removesearch)
 

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

##List DNS search list<a id="listsearch"></a>
    ~ # esxcli network ip dns search list
     DNSSearch Domains: localdomain
     ~ #

##Add DNS search list<a id="addsearch"></a>
    ~ # esxcli network ip dns search add -d intra.rtfmp.com
    ~ # esxcli network ip dns search list
    DNSSearch Domains: localdomain, intra.rtfmp.com
    ~ #

##Remove DNS search list<a id="removesearch"></a>
    ~ # esxcli network ip dns search add -d intra.rtfmp.com
    ~ # esxcli network ip dns search list
    DNSSearch Domains: localdomain, intra.rtfmp.com
    ~ #
