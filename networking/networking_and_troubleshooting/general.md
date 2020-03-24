# High Level Network Interface Tools

|  Concept | Description |
| --- | --- |
| MAC Address | Unique fingerprint of the network interface  | 
| IP Address | Unique address on the network | 
| Subnet | Separates the IP into network and host addresses | 
| Gateway | The connection leading outside of the local network | 
| DNS Host | Translates hostname into IP addresses | 
| DNS Domain | Lookup domnain for the host | 

Commands: 

```bash
# ip queries
ip addr show
# network device status
nmcli device show
# list active connections
ncmli c show
# list routes
routel
```

#### Routing

* A router is a layer 3 device, functioning at the IP level
* A router forwards data packets between networks
* The routing table is a static table mapping of the best path to a network destination 
* The routing table lists destinations and gateways for the networks the host belongs to
* Static routes can be used for connections that can't or shouldn't use the default gateway
* Border Gateway Protocol is routing protocol used to route traffic across the internet. It is a layer 4 protocol sitting on top of TCP
* An ASN is required to implement BGP peering. This is a special number assigned by IANA for use primarily with BGP that identifies each network on the internet

#### ARP

* Address Resolution Protocol is used for discovering the MAC address associated with a given network layer address (IP address)
* It is necessary to map Layer 3 (IP) addressing to Layer2 (MAC) addressing

```bash
# view the arp list
ip n
```

#### DNS

* DNS is a Layer 7 protocol used for discovering the IP address associated with a given domain name

A basic query: 
* A FDQN (fully qualified domain name) is the complete domain name for a specific host on the network
* When a computer wants to initiate a connection to an FQDN it needs to know where the host is on the network
* The computer will send a query to the DNS server, seeking to resolve the FQDN to an IP address, and then looks at the routing tables to determine where to send the request

```bash
# DNS lookup
dig www.google.com + trace
```