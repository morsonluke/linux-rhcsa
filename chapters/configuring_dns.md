# Configuring DNS

- [ ] Configure a caching-only name server
- [ ] Troubleshooting DNS client issues

Domain Name System is an OS/hardware-independent network service for determining the IP address of a system by proving its hostname and vice versa.

Determining the IP address of a hostname is referred to as a forward name resolution and other way around is predicatbly a reverse name resolution. 

BIND (Berkeley Internet Name Domain) is an open source implementation of DNS on UNIX/Linux OS. 

#### DNS Name Space 

To run an internet-facing server, a unique domain name needs to chosen and registered. These are accredited by ICANN (Internet Corporation for Assigned Names and Numbers). A list of authorized registrars is available [here](www.internic.net). 

The root servers sit at the top of the DNS hierarchy and there a a number of these root server with mirrors across the world. 

#### DNS Roles

| DNS Server Role | Description | 
| --- | --- |
| Primary | A primary or master DNS server has the authority over its domain and maintains that domain's original or master data. Zone data files are maintained on the primary server and they can be propagated to secondary servers |
| Secondary  | A secondary or slave DNS server also has authority for its domain and stores that domain's zone file - however these files are copies from the primary server | 
| Caching  | Has no authority and just caches it locally in the memory |
| Forwarding | Simply forwards an incoming query to a specified DNS server |
| DNS Client | A DNS client is used for initiating and sequencing hostname queries by referencing nameserver information defined in resolver configuration files |

