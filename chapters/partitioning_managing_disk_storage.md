# Partitioning and Managing Disk Storage

Disk can be divided into partitions. Each partition functions as if it were a separate hard disk. This would be useful to have say two OS. 

RHEL offers several tools for partitioning and managing disk storage. These include: 
* parted
* gdisk
* Logical Volume Manager (LVM)
* fdisk, sfdisk, cfdisk

The partition information is stored on the disk as the Master Boot Record (MBR) on BIOS and GUID Partition (GPT) on the UEFI based system. 

```bash
# see disks and partitions
lsblk
# list partition tables
fdisk -l
# print all mounted file systems in a tree like format
findmnt
cd /var/lib/libvirt/images
qemu-img create -f raw server1.example.local-virsh.img 2G
# attach to server1 using virsh
virsh attach-disk server1.example.local --source /var/lib/libvirt/images/server1.example.local-vish.img --target sda --persistent
```

#### Disk Utilities

Disk types examples. If there is a number after that is the partition number: 
* `/dev/sda1` - SCSI
* `/dev/hd1` - IDE
* `/dev/vda1` - virtual disk

This tool is used to carve up disks on RHEL system. 

```bash
parted /dev/sda1
# view current partition information
(parted) print
```

#### gdisk

`gdisk` utility is used to carve up disks using GPT format. MBR partitioning scheme supports a max of 15 paritions. In contrast GPT can hold up to 128. 

MBR also uses 32-bit logical addresses, which supports disk sizes up to 2TB where GPT format relies on 64-bit addresses so can support huge drives. 

```bash
gdisk /dev/nvme0n1
# follow the steps to create a new partition 
# once created run the command to update the partition info
partprobe /dev/nvme0n1
```

#### fdisk

`fdisk` works with partitions created using the traditional MBR partitioning scheme. 

```bash
# create a 2GB SCSI Hard Disk using the settings for the VM
fdisk /dev/nvme0n1
# select m to show help list and create a new partition
# see the partition table
Command (m for help): p
# create a partion and enter the defaults
```

* Mounting a partition makes its storage available starting at the specified directory

```bash
mkfs.xfs /dev/sdb1
fdisk -l /dev/sdb1
mount /dev/sdb1 /data
# update /etc/fstab file 
```

#### parted

* `parted` can be used to resize and copy partitions as well as the filesystems contained therein
* It is the foundation for multiple GUI-based partition managment tools include GParted

```bash
(parted) print
# get help 
(parted) help
```

#### Logical Volumes

* A volume group consists of a pool of storage taken from one or more physical volumes. This is divided into logical partitions called logical volumes
* Thin provisioning technology allows for the economic allocation and utlization of storage space

```bash
# identify disks and partitions we can use for LVM 
lvmdiskscan
```

#### Creating a Logical Volume

1. Create paritions of type Linux LVM

```bash
# create a partition
fdisk /dev/sdb
# change the partition type using fdisk (8e)
fdisk -l /dev/sdb
inform OS of partition table change
partprobe /dev/sdb
```

2. Create a physical volume using `pvcreate`

```bash
pvcreate /dev/sdb1
# see information about physical volumes
pvs
```

3. Create volume group using the physical volumes with `vgcreate`

```bash
vgcreate database /dev/sdb1
```

4. Create logical volumes using lvcreate

```bash
lvcreate -L 1G -n sql database
# list the logical volumes
lvs
# make filesystem
mkfs.xfs /dev/database/sql
mount /dev/database/sql /sql/
```

5. Update the UUID of logical volumne name in `/etc/fstab` to set permanetly 

```bash
vi /etc/fstab
```
