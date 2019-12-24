# Partitioning and Managing Disk Storage

Disk can be divided into partitions. Each partition functions as if it were a separate hard disk. This would be useful to have say two OS. 

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
cd /var/lib/libvirt/images
qemu-img create -f raw server1.example.local-virsh.img 2G
# attach to server1 using virsh
virsh attach-disk server1.example.local --source /var/lib/libvirt/images/server1.example.local-vish.img --target sda --persistent
```

#### Disk Utilities

Disk types examples. If there is a number after that is the partition number: 
* /dev/sda1 - SCSI
* /dev/hd1 - IDE
* /dev/vda1 - virtual disk

This tool is used to carve up disks on RHEL system. 

```
parted /dev/sda1
# view current partition information
(parted) print
```

#### gdisk

`gdisk` utility is used to carve up disks using GPT format. 

#### fdisk

```
# create a 2GB SCSI Hard Disk using the settings for the VM
fdisk /dev/sdb
# select me to show help list and create a new partition
n
# accept defaults and accept changes
w
```

* Mounting a partition makes its storage available starting at the specified directory

```
mkfs.xfs /dev/sdb1
fdisk -l /dev/sdb1
mount /dev/sdb1 /data
# update /etc/fstab file 
```

#### Logical Volumes

* A volume group consists of a pool of storage taken from one or more physical volumes. This is divided into logical partitions called logical volumes
* Thin provisioning technology allows for the economic allocation and utlization of storage space

```
# identify disks and partitions we can use for LVM 
lvmdiskscan
```

#### Creating a Logical Volume

1. Create paritions of type Linux LVM

```
# create a partition
fdisk /dev/sdb
# change the partition type using fdisk (8e)
fdisk -l /dev/sdb
inform OS of partition table change
partprobe /dev/sdb
```

2. Create a physical volume using `pvcreate`

```
pvcreate /dev/sdb1
# see information about physical volumes
pvs
```

3. Create volume group using the physical volumes with `vgcreate`

```
vgcreate database /dev/sdb1
```

4. Create logical volumes using lvcreate

```
lvcreate -L 1G -n sql database
# list the logical volumes
lvs
# make filesystem
mkfs.xfs /dev/database/sql
mount /dev/database/sql /sql/
```

5. Update the UUID of logical volumne name in `/etc/fstab` to set permanetly 

```
vi /etc/fstab
```
