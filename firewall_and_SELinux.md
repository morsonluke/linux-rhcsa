# Controlling Access through Firewall and SELinux

* A firewall is a protective layer that is configured between private and a public network to segregate traffic
* RHEL comes with a host-based packet-filtering firewall software called iptables that communicates with the netfilter module in the kernel for policing the flow of data packets

####  iptables

Managing iptables can be done with firewalld or without. In the presence of both  only one can be used at a time.

* CLI: firewall-cmd || GUI firewall-config - > firewalld daemon -> iptables command -> netfilter kernel module

See which packages are installed 
```bash
    $ yum list installed | egrep 'iptables|firewalld'
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

Add and activate iptables Rules
```bash
    # remove all existing rules
    $ iptables -F
    # allow inbound  HTTP traffic on port 80
    $ iptables -t filter -A INPUT -p tcp --dport 80 -j ACCEPT
    # reject outbound ICMP traffic
    $ iptables -A OUTPUT -p icmp -j DROP
    # allow incoming FTP connection on port 21
    $ iptables -I INPUT -m state --state NEW -p tcp --dport 21 -j ACCEPT
    # save rules in /etc/sysconfig/iptables
    service iptables save
```

#### firewall-cmd

In order to use firewalld operations we need to deactivate iptables service

```bash
    # systemctl stop iptables
    # systemctl enable firewalld
```
