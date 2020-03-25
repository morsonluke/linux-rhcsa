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
````