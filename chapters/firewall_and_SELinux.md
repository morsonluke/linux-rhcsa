# Controlling Access through Firewall and SELinux

* A firewall is a protective layer that is configured between private and a public network to segregate traffic. It enables users to control network traffic on host machines by defining a set of firewall rules
* RHEL comes with a host-based packet-filtering firewall software called `iptables` that communicates with the `Netfilter` module in the kernel for policing the flow of data packets
* `Netfilter` is a set of hooks inside the Linux kernel that allows the kernel modules to register callback functions with the network stack. A registered callback function is then called back for every packet that traverses the respective hook within the network stack

####  iptables and firewalld

* The `iptables` tool is the basic foundation that is used by other services to manage systems firewall rules
* RHEL comes with the `firewalld` daemon and the `iptables` service
* `firewalld` is a firewall service daemon that provides a dynamic customizable host-based firewall with a D-Bus interface
* The firewalld service can be interact with using the graphical utility `firewall-config` or the command-line client `firewall-cmd`
* `firewalld` uses the concept of zones and services. Zones are predifined set of rules. Network interfaces and sources can be assigned to a zone
* iptables and firewalld both rely on the Netfilter system with the Linux kernel to filter packages. Whereas iptables is based on "chain of filter rules" to block or forward traffic, firewalld is "zone-based". Netfilter provides a set of hooks for actions on network activities

| IPTABLES | Description |
| --- | --- | 
| `Filter Table` | Used for determining if a packet is  permitted to continue or be denied  |
| `NAT Table` | Used for performing NAT rules by determining how to modify a packet's source or destination address to effect routing |
| `Mangle Table` | Used for altering the IP headers of a packet |
| `Raw Table` | Used for opting out of connection tracking |
| `Security Table` | Used for setting SELinux security context values on packets or connections |

See which packages are installed 

```bash
yum list installed | egrep 'iptables|firewalld'
# install packages
yum install firewalld firewall-config
```

Stop firewalld and enable iptables

```bash
systemctl enable firewalld
systemctl start firewalld
systemctl status firewalld -l
systemctl stop firewalld
systemctl disable firewalld
# ensure firewalld is not started by accessing the firewall D-Bus interface 
systemctl mask firewalld
# enable iptables
systemctl enable iptables
systemctl start iptables
systemctl status iptables
# see the rules for iptables
iptables -L
```

Add and activate iptables Rules

```bash
# remove all existing rules
iptables -F
# the general pattern follows...
iptables -t tabletype <action_direction> <packet_pattern> -j <what_to_do>
# allow inbound  HTTP traffic on port 80
iptables -t filter -A INPUT -p tcp --dport 80 -j ACCEPT
# reject outbound ICMP traffic
iptables -A OUTPUT -p icmp -j DROP
# allow incoming FTP connection on port 21
iptables -I INPUT -m state --state NEW -p tcp --dport 21 -j ACCEPT
# save rules in /etc/sysconfig/iptables
service iptables save
```

#### firewall-cmd

```bash
# deactivate iptables service
systemctl stop iptables
systemctl enable firewalld
systemctl start firewalld
# get default zone
firewall-cmd --get-default-zone
# list all zones
firewall-cmd --get-zones 
# see details for a zone
firewall-cmd --zone=block --list-all
# list all the configured interfacs and services
firewall-cmd --list-all
# Allow HTTP traffic on default port
firewall-cmd --permanent --add-service=http
# Allow traffic on 8443 for TCP
firewall-cmd --add-port=8443/tcp
# Active the rule
fireall-cmd --reload
# Confirm changes
firewall-cmd --list-services
firewall-cmd --list-ports
```

* A zone is made up of a group of source network addresses and interfaces, plus the rules to process the packets that match those source addresses and network interfaces

Add telnet:

```bash
# see the definitions for the different zones
man firewalld.zones
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

Add new zones: 

```bash
# create two new zones
firewall-cmd --permanent --new-zones=myPublicWeb
firewall-cmd --permanent --new-zones=myPrivateDNS
# reload for the changes to take effect
firewall-cmd --reload
# see the zones
firewall-cmd --get-zone
```

* Port forwarding is an application of NAT that redirects a communication request from one address and port number to another
* Ports are logical devices that enable an OS to recieve and distinguish network traffic and forward it accordingly to system services

```bash
# list allowed ports
firewall-cmd --list-ports
# forward to another port
firewall-cmd --permanent --add-masquerade
# add rule
firewall-cmd --permanent --add-forward-port=port=5555:proto=tcp:toport=80
```

Audit access from a client/server on port 80:

```bash
# check the status of iptables
systemctl status iptables
# verify the host is listening on port 80
ss -lntp | grep :80
# check connection from the client 
curl 10.0.1.10
# allow access from the client
iptables -I INPUT -p tcp -s 10.0.1.11 --dport 80 -j ACCEPT
# save the settings
service iptables save
```

Audit access in a client/server setup: 

```bash
# check something is listening on port 80
ss -lntp | grep :80
curl -I localhost
systemctl status firewalld
# see the firewall rules
firewall-cmd --list-all
# create a new zone
firewall-cmd --permanent --new-zone=api
firewall-cmd --reload
# add http service
firewall-cmd --permanent --zone=api --add-service=http
firewall-cmd --permanent --zone=api --add-source=10.0.1.11
firewall-cmd --reload
```

#### SELinux

* Stands for Security Enhanced Linux and is an implementation of the Mandatory Access Control (MAC) architecture
* SELinux adds to the Discrentionary Access Control system (DAC) which restricts object access via an access policy determined by an object's owner group and/or subjects (ie. what we'd see if we run ls -l file1)
* SELinux decisions are stored in a cache area referred to as Access Vector Cache (AVC)
* SELinux security model is based on subjects, objects and actions
* SELinux policy is administratively-defined, enforced system-wide, and is not set as user discretion

| `/etc/selinux/config` | Description | 
| --- | --- |
| SELINUX | SELinux status; can be set to `enforcing`, `permissive` or `disabled` | 
| SELINUXTYPE | Specifies level of protection; set to `targeted` by default. The alternative is `mls` which is associated with multi level security | 

```bash
# see the state of SELinux
getenforce
sestatus
# see the root user has no restrictions
id -Z unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
# see the other SeLinux users
yum install setools-console
seinfo -u
# determine the context of a process
ps -eZ
ps  auxZ | grep httpd
# see context information for a file
ll -Z /etc/passwd
# change to permissive in order to debug if an issue is SELINUX related for example
setenforce permissive
# add a new context
semanage fcontext -a -t httpdsys '/content(/.*)?'
restorecon -R /content
```

Troubleshoot issues: 

```bash
yum install setroubleshoot-server
# get troubleshooting information 
sealert -a /var/log/audit/audit.log
```

When a scenario is blocked by SELINUX, the `/var/log/audit/audit.log` is the first place to look. The `ausearch` tool is used to search the audit logs.

```bash
ausearch -m AVC,USER_AVC,SELINUX_ERR,USER_SELINUX_ERR -ts recent
```

#### GPG2

The computer standard for file encryption and signature services is known as **Pretty Good Privay** (PGP). The open-sourc version is GNU Privacy Guard  (GPG). The RHEL7 version is GPG2. 

GPG2 has three cryptographic mechanism that are all public/private key mechanis: RSA, DSA, ElGamal

```bash
# there was an issue with no "entropy" being create on the VM so to allow for keys to be created
sudo yum install rng-tools
sudo rngd -r /dev/urandom
# generate a key and work through the prompts
gpg --gen-key
# list the keys
gpg --list-keys
# export the key
gpg --export First Last > gpg.pub
``` 
We can also simply encrypt a file:

```bash
gpg --yes --batch --passphrase=pssphrase --cipher-algo AES256 -c gpg.json
```
