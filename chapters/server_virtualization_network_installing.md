# Server Virtualization and Network Installing

Server virtualization allows a physical computer to host several virtual machines, each of which acts as a standalone computer running an OS.

Virtualization software deployed directly on bare-metal host machine is referred to as the hypervisor software. KVM (Kernel-based Virtual Machine) is part of the Linux Kernel and comes as a native hypervisor with RHEL7. It enables mapping between physical CPUs on the host machine and virtual CPUs alloted to virtual machines.

Quick Emulator (QEMU) then uses the physical-to-virtual CPU mappings provided by KVM. libvirt is the virtualization management library.

#### Packages Associated with Virtualization

| Package | Description |
| ---     |       ---   | 
| `qemu-kvm` |  The main KVM packages      |
| `libvirt` |  Service to manage hypervisors   |
| `libvirt-client` | virsh command and clients APIs to manage VMs |
| `virt-install` | CLI tools for creating VMs   |
| `virt-manager` | GUI VM admin tool |
| `virt-top` | Display virtualization statistics   |
| `virt-viewer` | Graphical console to connect to VMs  |

To enable virtualization in VMWare on a host:
* System Settings -> Processors & Memory -> "Enable hypervisor application in this virtual machine"

```bash
# check if the processor supports virtualization
lscpu | grep -i virtualization
# check for flag vmx (intel processor) 
grep vmx /proc/cpuinfo
# check kernel modules are loaded
lsmod | grep kvm
```

```bash
# at this point we could set Selinux to permissive mode
setenforce 0
# install virtualization packages
yum install virt-manager libvirt libvirt-client
# rather than memorising these packages see groups
yum grouplist hidden
# see what the group includes
yum group info "Virtualization Client"
# install
yum groupinstall "Virtualization Client"
yum groupinstall "Virtualization Tools"
yum groupinstall "Virtualization Platform"
# restart the virtualization daemon
systemctl restart libvirtd
```

#### Virtual Network Switch

A virtual network switch is a libvirt-constructed software switch to allow the virtual machines to be able to communicate with the host and among one another. The host and virtual machines see this switch as a virtual network interface. 

```bash
systemctl status libvirtd
# see configuration
ip addr show vibr0
```

The default mode of operation for virbr0 is NAT with IP masquerading. NAT allows the network traffic of guest operating system to access external networks via the IP address of the host machine. 

#### Virtualization Management Tools

* The default hypervisor and virtual machine management software is libvirt. The virt-manager is the graphical equivalent of virt-install and virsh. 

```bash
# open virt-manager graphical tool
virt-manager
```

#### Define a NAT Virtual Network Using virsh

```bash
yum install libvirt
# create rhnet_virsh.xml definition in /root
virsh net-define /root/rhnet_virsh.xml
# set automatic start up
virsh net-autostart rhnet_virsh
# start new virtual network
virsh net-start rhnet_virsh
# list all virtual networks
virsh net-list
# see details of virtual network
virsh net-info rhnet_virsh
```

#### Create a Storage Pool and Volume Using virsh

```bash
# view available storage pools
virsh pool-list 
# makde the directoty
mkdir /var/lib/libvirt/rhpol_virsh
# define storage pool as dir
virsh pool-define-as rhpol_virsh dir - - - - /var/lib/libvirt/rhpol_virsh
# set automatic start-up
virsh pool-autostart rhpol_virsh
# start pool
virsh pool-start rhpol_virsh
# list pool
virsh pool-list
# view deatails of the pool
virsh pool-info rhpol_virsh
# create volume
virsh vol-create-as rhpol_virsh rhvol_virsh 20G
# list available volumes
virsh vol-list rhpol_virsh
```

#### Configure FTP Installation Server

FTP is a standard networking protocol for transferring file between systems. In RHEL there is a version called very secure FTP or vsFTP which allows us to enable, disable and set security contorls on incoming service requests. The vsFTP daemon `vsftpd` communicates on port 21. 

```bash
yum - y install vsftpd
# create a directory for storing the installation files
mkdir /var/ftp/pub/rhel8
# copy directory structure from the filesystem that has the DVD mounted
cd /media && find . | cpio -pmd /var/ftp/pub/rhel8
# add firewall rule
firewall-cmd --permanent --add-service=ftp
# reload
firewall-cmd --reload
# start and enable the service
systemctl start vsftpd
# may need to set SELinux
chcon -R -t public_content_t /var/ftp/
# check any errors
journalctl -xe
```

It seems like using the DVD is not an option so in one test the .iso file was copied to the VM

```bash
scp ~/Documents/rhel-8.1-x86_64-dvd.iso {username}@192.168.98.130:~/
```

Replace /media repo with FTP 

```bash
# remove any .repo configuration 
rm /etc/yum.repos.d/dvdinstall.repo
# eject the mount point from the dvd
eject /media
# create a new definition file and add config
vi /etc/yum.repos.d/ftp.repo
# check it has worked
yum clean all 
yum repolist
```

#### KVM 

* Configuration files can be found in `/etc/libvirt` and `/var/lib/libvirt`
* We can view XML configuration files in `/etc/libvirt/qemu`

```bash
# destroy a VM using virsh
virsh destroy server1.example.com
# delete associated configuration files
virsh undefine server1.example.com --remove--all-storage
```

```bash
# virsh starts a front end to existing KVM VMs
(virst #) list 
# start a VM
virsh start server1.example.com
# shutdown a VM
virsh shutdown 
```

#### The wget Utility 

`wget` is a non-interactive file download utility that allows you to retrieve a single file or entire directory from FTP, HTTP(S).

```bash
wget -d www.redhat.com
```

#### Maintaining and Managing Log Files

A script called `logrotate` in `/etc/logrotate.d` manages the rotation of logfiles by source `/etc/logrotate.conf`

#### The Journal 

```bash
# see verbose output
journalctl -o verbose
```