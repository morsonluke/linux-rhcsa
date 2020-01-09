# Administering Network Interfaces and Network Clients

```bash
    # find the hostname in a number of different ways
    $ hostname
    $ hostnamectl | grep hostname
    $ uname -n
    $ cat /etc/hostname
    # nmcli is a command line tool for controlling NetworkManager 
    $ nmcli general hostname
```

An IP address is four dot-separated octets divided into a network portion and a node portion. Network addresses are classified into three classes (uninspiringly) A, B & C.

Subnetting is a method by which a large network address space can be divided into several smaller ones called subnets. 

```bash
# see ip address 
ip addr
# see protocols
cat /etc/protocols
# common services and ports listened on 
cat /etc/services | less
# see contents of ifcfg-eth0 file. The network manager stores the interface config files here
cat /etc/sysconfig/network-scripts/ifcfg-eth0
```

* The  `/etc/nsswitch.conf` file specifies the database search priorities for "everything"
* The `/etc/hosts` file can be used for small internal networks for hostname to IP resolution. DNS is used for larger networks
* `/etc/resolv.conf` is the standard file for documenting the location of DNS servers

The ping command send out 64-byte Internet Control Message Protocol test packets.

```
# send two packets to google 
ping -c2 8.8.8.8
```

A network protocol called ARP maps the Ethernet address to the destination node's IP address.

```bash
  # network configuration files are stored here
   /etc/sysconfig/network-scripts/
```

A number of commands are available to administer network interfaces. 

| Command | Description |
| --- | --- |
| ifdown | Activate\Deactivate interface | 
| ip | Admin interfaces, routing ...  |
| nm-connection-editor  | Graphical tool for interface admin   |
| Network Settings | Graphical connection status and admin tool  |
| nmcli | CLI tool for interface admin  |
| nmtui | Text-based tool for interface admin |

The ip command has replaced a number of now obslete commands from RHEL 7;

| Obselete | Equivalent |
| -- | --- | --- |
| ifconfig | ip [-s] link,  ip addr | 
| ifconfig eth0 192.168.122.150 netmask 255.255.255.0 | ip addr add 192.168.122.150/24 dev eth0 |
| arp | ip neigh | 
| route, netstart -r | ip route |
| netstat -tuplpa | ss -tupna |

#### NTP Client

Network Time Protocol is a networking protocol for syncronising the system clock with a reliable remote time clock. 

```bash
yum -y install ntp system-config-date
# review the servers configured
grep ^server /etc/ntp.conf
# start the service
systemctl enable ntpd
systemctl start ntpdn
# see the output
ntpq -p
```

The NTP client functionality can also be configured using the **System-Config-Date** graphical tool. 

#### OpenLDAP

Lightweight Directory Access Protocol is a networking protocol for obtaining centrally-stored information over the network. OpenLDAP is suitable for a write-once-read-many-times style application. 

Benefits include: uniform information, centralized storage for information and user authentication

* Distinguides Name (DN): Uniquely identifies an entry in the directory tree. It is a sequence of **relative distinguished names** separated by commas e.g. cn=prn1,ou=Printers, c=ca
* The relative distinguished names represent individual componenets of a DN
* We have the option of choosing between auth services: Syste Security Services Daemon (SSSD) and Name Service Local Cachine Daemon (NSLCD)

LDAP client configuration can be done using the authconfig command. "

```bash
  # install dependencies
  yum -y install openldap openldap-clients nss-pam-ldapd sssd authconfig
  # attempt setup
  authconfig --enableldap  --enableldapauth --ldapserver=ldap://labipa.example.local --enablesssd --ldapbasedn="dc=example,dc=local" --updatex
  # enable services
  systemctl enable sssd
  systemctl start sssd
  # check id of user
  id lisa
```