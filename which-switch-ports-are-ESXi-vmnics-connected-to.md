When I build ESXi host, it is not always clear which vmnic is connected to which ports on the physical switch nor which vmnic should be used for what purpose. When I am in this situation, I turn to CDP information. It does not always give a definite answer but it helps me ask the right question to the Network Admin.

There are different ways to view CDP information in ESXi. VMware has a nice article here. My favorite way is to use this command

    # vim-cmd hostsvc/net/query_networkhint --pnic-name=vmnic[xx
    

Sample Output

Access Port

Trunk Port
