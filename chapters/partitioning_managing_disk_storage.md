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
# create a partion and enter the defaults making a 500MiB size partition 
Last sector, +sectors or +size{K,M,G} (2048-2097151, default 2097151): +500M
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

```bash
# create a partition
fdisk /dev/sdb
# change the partition type using fdisk (8e)
fdisk -l /dev/sdb
# inform OS of partition table change
partprobe /dev/sdb
```

```bash
 #Create a physical volume using `pvcreate`
pvcreate /dev/sdb1
# see information about physical volumes
pvs
pvdisplay
```

```bash
# Create volume group using the physical volumes
vgcreate database /dev/sdb1
# see information about volume groups
vgs
vgdisplay
```

```bash
# Create logical volumes using lvcreate
lvcreate -L 1G -n sql database
# list the logical volumes
lvs
lvdisplay
# make filesystem
mkfs.xfs /dev/database/sql
mount /dev/database/sql /sql/
```
 
```bash
# check for UUID
blkid
# Update the UUID of logical volumne name in `/etc/fstab` to set permanetly
vi /etc/fstab
UUID=8ac075e3-1124-4bb6-bef7-a6811bf8b870 /mnt/sdb1 xfs defaults 0 0
# check mounted correctly
umount /dev/sdb1
# check /etc/fstab file is correct
mount -a
```

We can grow a mount point: 

```bash
lvextend -L+200M /dev/RHCSA/pinehead
# resize the filesystem
xfs_growfs /mnt/lvol
```

Grow a current mount point: 

```bash
# add to the Logical Volume
pvcreate /dev/nvme3n1
vgextend vdoDev /dev/nvme3n1
lvextend -l +100%FREE /dev/vdoDev/vdoLV

# extend physical disk and logical size
vdo growPhysical --name=LA
vdo growLogical --name=LA --vdoLogicalSize=180G

# grow the filesystem 
partprobe
xfs_growfs /mnt/vdo
```

#### fstab & mtab files

The default in RHEL is to use UUIDs to mount non-LVM filesystems. They can represent a partition, a logical volume, or a RAID array. We can verify how partitions are actually mounted ni the `/etc/mtab` file. 

``` bash
UUID=8ac075e3-1124-4bb6-bef7-a6811bf8b870 /                       xfs     defaults        0 0
# mount something like a CD drive as removable media
/dev/sr0 /cdrom auto ro,noauto,users 0 0
```

| Field Name | Description |
| --- | --- |
|  Device | Device to be mounted can be UUID or device path |
| Mount Point | Directory to be mounted   |
| Filesystem Format | xfs, ext2, msdos, nfs etc.  |
| Mount Options | Various options, generally set to default |
| Dump Value | Either 0 or 1. If the dump command is used to back up the fileystem, this field controls the filesystem to be dumped |
|  Filesystem Check Order | Order in which they are checked by the `fsck` command | 

mtab virtual filesystems:

* `tmpfs` - the virtual memory filesystem uses both RAM and swap space
* `devpts` - pseudo-terminal devices
* `sysfs` - dynamic information about system devices
* `proc` - dynamically configurable options for changing the behaviour of the kernel
* `cgroups` - control group feature of the Linux kernel, which allows you to set limits on the system resource usage for a process or a group of processes

#### Automounter

The automount daemon, or `autofs` can automatically mount a specific filesystem as needed. It can umnount a filesystem automatically after a fixed period of time. 

```bash
# install autofs
yum install autofs
systemctl start autofs
systemctl enable autofs
# list the configuration files
ls -ltr /etc/auto*
```

#### Disk Compression (Virtual Disk Optimizer)

* VDO is a method of providingdeduplication, compression, and thin provisioning
* VDO is applied to a block device and then you do normal disk operations to that VDO device

```bash
yum install vdo kmod-kvdo
# install and ensure it start after reboot
systemctl start vdo.service && systemctl enable vdo
# creaet a VDO volume
vdo create --name=NAME --device/dev/device --vdoLogicalSiz=SIZE --sparseIndex=enabled --vdoSlabSize=32G
# create filesystetm
mkfs.xfs -K /dev/mapper/NAME
# make sure the system registers the new device node
udevadm settle
# mount
mount /dev/mapper/NAME /mount/point
# see the stats
vdostats --human-readable
```

Labs exercise: 

```bash
# ensure a dense index deduplication
sudo vdo create --name=Containerstorage --device=/dev/nvme1n1
 --vdoLogicalSize=100G --sparseIndex=disabled
 # ensure deduplication is disabled
vdo create --name=WebsiteStorage --device=/dev/nvme2n1 --vdoLogicalS
ize=60G --deduplication=disabled
 # setup  filesystem
mkfs.xfs -K /dev/mapper/Containerstorage && udevadm settle
# create mount point and mount 
mount /dev/mapper/Containerstorage /mnt/containers/
# add to /etc/fstab
/dev/mapper/ContainerStorage /mnt/containers xfs defaults,_netdev,x-systemd.device-timeout=0,x-systemd.requires=vdo.service 0 0
```