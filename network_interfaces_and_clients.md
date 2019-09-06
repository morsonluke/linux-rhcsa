# Administering Network Interfaces and Network Clients

```
# find the hostname 
hostname
hostnamectl | grep hostname
uname -n
cat /etc/hostname
```

Subnetting is a method by which a large network address space can be divided into several smaller ones called subnets. 

```
# see ip address 
ip addr
# see protocols
cat /etc/protocols
# common services and ports listened on 
cat /etc/services | less
# see contents of ifcfg-eth0 file
cat /etc/sysconfig/network-scripts/ifcfg-eth0
```

The `/etc/hosts` file can be used for small internal networks for hostname to IP resolution.

```
# send two packets to google
ping -c2 8.8.8.8
```

#### NTP Client

Network Time Protocol is a networking protocol for syncronising the system clock with a reliable remote time clock. 

```
yum -y install ntp
# review the servers configured
grep ^server /etc/ntp.conf
# start the service
systemctl enable ntpd
systemctl start ntpdn
# see the output
ntpq -p
```

#### OpenLDAP

Lightweight Directory Access Protocol is a networking protocol for obtaining centrally-stored information over the network. OpenLDAP is suitable for a write-once-read-many-times style application. 

