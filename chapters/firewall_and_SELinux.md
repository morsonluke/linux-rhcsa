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
    $ service iptables save
```

#### firewall-cmd

In order to use firewalld operations we need to deactivate iptables service

```bash
    $ systemctl stop iptables
    $ systemctl enable firewalld
    $ systemctl start firewalld

    $ firewall-cmd --get-default-zone
    
    # Allow HTTP traffic on default port
    $ firewall-cmd --permanent --add-service=http
    # Allow traffic on 8443 for TCP
    $ firewall-cmd --add-port=8443/tcp
    # Active the rule
    $ fireall-cmd --reload
    # Confirm changes
    $ firewall-cmd --list-services
    $ firewall-cmd --list-ports
```
#### SELinux

* Stands for Security Enhanced Linux and is an implementation of the Mandatory Access Control (MAC) architecture
* SELinux decisions are stored in a cache area referred to as Access Vector Cache (AVC)

```bash
    # see the root user has no restrictions
    $ id -Z --> unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
    # see the other SeLinux users
    $ yum install setools-console
    $ seinfo -u
    # determine the context of a process
    $ ps -eZ
    # see context information for a file
    $ ll -Z /etc/passwd
    # see the state of SELinux
    sestatus
```