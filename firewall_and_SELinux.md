# Controlling Access through Firewall and SELinux

* A firewall is a protective layer that is configured between private and a public network to segregate traffic
* RHEL comes with a host-based packet-filtering firewall software called iptables that communicates with the netfilter module in the kernel for policing the flow of data packets

####  iptables

Managing iptables can be done with firewalld or without. In the presence of both  only one can be used at a time.

* CLI: firewall-cmd || GUI firewall-config - > firewalld daemon -> iptables command -> netfilter kernel module

See which packages are installed 
```bash
    $ yum list installed | egrep 'iptables|firewalld
```

Stop firewalld and enabnle iptables
```bash
    $ systemctl enable firewalld
    $ systemctl start firewalld
    $ systemctl status firewalld -l
    $ systemctl stop firewalld
    $ systemctl disable firewalld

    $ systemctl status firewalld
    $ systemctl enable iptables
    $ systemctl start iptables
    $ systemctl status iptables
    # see the rules for iptables
    iptables -L
```
