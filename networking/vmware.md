### Networking inside a VM

* VMWare Fusion VMs talk to your network using a virtual network adapter
* The VM guest OS believes it is equipped with a wired Ethernet card
* VMWare has the following network adaptor modes: NAT, Bridged, Host-only

#### Bridged

* The network is extended without using a router. The local wired or wireless network is effectively extended to the VM. The VM becomes a peer of all other computers on the network
* The VM will retain its own fully independent network identity

#### NAT

* NAT mode places the VM in an isolated virtual network
* In NAT mode the VM will get the IP address from a DHCP server supplied by VMWare Fusion
* From the internet perspective the VM is sharing the Mac's IP address

#### Host-only Mode

* In host-only mode the VM is locked out of the local network. The network world is wholly within your mac
* The VM will get its IP address from a DHCP server supplied by VMWare Fusion 