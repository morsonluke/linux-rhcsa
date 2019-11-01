# Partitioning and Managing Disk Storage

RHEL offers several tools for partitioning and managing disk storage. These include: 
* parted
* gdisk
* Logical Volume Manager (LVM)
* fdisk, sfdisk, cfdisk

The partition information is stored on the disk as the Master Boot Record (MBR) on BIOS and GUID Partition on the UEFI based system. 

```
# see disks and partitions
lsblk
```

```
# install virtualization packages
yum install qemu-kvm qemu-img libvirt libvirt-client
cd /var/lib/libvirt/images
qemu-img create -f raw server1.example.local-virsh.img 2G
# attach to server1 using virsh
virsh attach-disk server1.example.local --source /var/lib/libvirt/images/server1.example.local-vish.img --target sda --persistent
```

#### Disk Utilities

This tool is used to carve up disks on RHEL system. 

```
parted /dev/sda1
# view current partition information
(parted) print
```

`gdisk` utility is used to carve up disks using GPT format. 

#### Logical Volumes

A volume group consists of a pool of storage taken from one or more physical volumes. This is divided into logical partitions called logical volumes. 

Thin provisioning technology allows for the economic allocation and utlization of storage space.

```
# identify disks and partitions we can use for LVM 
lvmdiskscan
```