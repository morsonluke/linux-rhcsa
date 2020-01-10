# Controlling Access through Firewall and SELinux

* A firewall is a protective layer that is configured between private and a public network to segregate traffic
* RHEL comes with a host-based packet-filtering firewall software called iptables that communicates with the netfilter module in the kernel for policing the flow of data packets

####  iptables

* The `iptables` tool is the basic foundation that is used by other services to manage systems firewall rules
* RHEL comes with the firewalld daemon and the iptables service
* The firewalld service can be interact with using the graphical utility `firewall-config` or the command-line client `firewall-cmd`
* iptables and firewalld both rely on the Netfilter system with the Linux kernel to filter packages. Whereas iptables is based on "chain of filter rules" to block or forward traffic, firewalld is "zone-based"

See which packages are installed 

```bash
    $ yum list installed | egrep 'iptables|firewalld'
```

Stop firewalld and enable iptables

```bash
    $ systemctl enable firewalld
    $ systemctl start firewalld
    $ systemctl status firewalld -l
    $ systemctl stop firewalld
    $ systemctl disable firewalld
    # enable iptables
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
    # the general pattern follows...
    iptables -t tabletype <action_direction> <packet_pattern> -j <what_to_do>
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

```bash
    # deactivate iptables service
    $ systemctl stop iptables
    $ systemctl enable firewalld
    $ systemctl start firewalld
    # get default zone
    $ firewall-cmd --get-default-zone
    # list all the configured interfacs and services
    $ firewall-cmd --list-all
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

* A zone is made up of a group of source network addresses and interfaces, plus the rules to process the packets that match those source addresses and network interfaces

Add telnet:

```bash
    # see active services
    nmap localhost
    # ensure firewalld is running
    systemctl status firewalld
    # install yum and start
    systemctl start telnet.socket
    # see current settings
    firewall-cmd --list-all
    # allow telnet traffic through
    firewall-cmd --permanent --add-service=telnet
    # apply the changes
    firewall-cmd --reload
```

#### SELinux

* Stands for Security Enhanced Linux and is an implementation of the Mandatory Access Control (MAC) architecture
* SELinux decisions are stored in a cache area referred to as Access Vector Cache (AVC)
* SELinux security model is based on subjects, objects and actions

| `/etc/selinux/config` | Description | 
| --- | --- |
| SELINUX | SELinux status; can be set to `enforcing`, `permissive` or `disabled` | 
| SELINUXTYPE | Specifies level of protection; set to `targeted` by default. The alternative is `mls` which is associated with multi level security | 

```bash
    # see the root user has no restrictions
    $ id -Z unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
    # see the other SeLinux users
    $ yum install setools-console
    $ seinfo -u
    # determine the context of a process
    $ ps -eZ
    # see context information for a file
    $ ll -Z /etc/passwd
    # see the state of SELinux
    getenforce
    sestatus
    # change to permissive
    setenforce permissive
```