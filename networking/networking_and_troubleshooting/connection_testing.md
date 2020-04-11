# Connection Testing 

| OSI Layers | Potential Tools |
| --- | --- |
| Layer 2: switch and VLAN config, MAC addressing and IP conflicts | `arping` |
| Layer 3: Network (IP) - addressing, routing, network auth | `ping`, `traceroute`, `tracepath`  |
| Layer 4: Transport - blocked ports, firewalls |  `ss`, `telnet`, `tcpdump`, `nc` |
| Layer 5-7: Application or service function | `dig`, `service tools` |

There are a number of tools and steps that can be taken with connection testing depending on the issue: 

```bash
# try and curl a server
curl -I 10.0.1.19
# check for Apache webserver running
ss -lntp | grep :80
# check firewall services
systemctl status {firewalld,iptables}
# see firewall settings
firewall-cmd --list-all
# modify the firewall 
firewall-cmd --permanent --add-service=http
firewall-cmd --reload
# check route tables
route -n
# see packets
tcpdump -i eth0 -n src host 10.0.1.16
```

#### Packet Captures

```bash
# see traffic that isn't related to SSH 
sudo tcpdump -ni eth0 port not 22
```


