# Network Interface Tools

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