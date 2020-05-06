#### Network Interfaces

Set a static IP address

```bash
# see basic connection detail  
nmcli c
# see more comprehensive information
nmcli con show "System eth0"
# see device info
nmclid d show
# use nmcli to modify 
sudo nmcli con mod System\ eth0 ipv4.method manual ipv4.addresses 10.0.1.10/24 ipv4.gateway 10.0.1.1 ipv4.dns 10.0.0.2 ipv4.dns-search ec2.internal
# restart the network 
sudo systemctl restart network
```

Provision more IP addresses on an instance: 

```bash
# see network information 
nmcli  
# add IP addresses
sudo nmcli c mod System\ eth0 ipv4.addresses 10.0.1.15/24,10.0.1.20/24
# restart network 
sudo systemctl restart network
# confirm new IP addresses under inet4
nmcli
```

#### NIC Bonding and Teaming

Teaming: 
* Mechhanism to group multiple NICs into one logic one at the L2 layer
* Support for IPv6 link monitoring
* Able to work with D-Bus and Unix Domain Sockets
* Load balancing for LACP support
* Leverages NetworkManager and associated tools

```bash
# install teamd package
sudo yum -y install teamd bash-completion
# look at the overall configuration 
nmcli
# create the team connection
sudo nmcli con add type team con-name Team0 ifname team0
# modify to include the static IP and gateway
sudo nmcli con mod Team0 ipv4.address 10.0.1.15/24 ipv4.gateway 10.0.1.1 ipv4.method manual
# add the slave interface
sudo nmcli con add type team-slave ifname eth1 con-name Slave1 master team0
# verify the state of the team
sudo teamdctl team0 state
```

Bonding: 
* Provides a method for aggregating multiple netork interfaces into a single logical bonded interface. 
* Typically used to increase link speed or do a failover on the server
* Works in a virtual environment