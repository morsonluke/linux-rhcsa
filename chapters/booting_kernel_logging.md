# Booting, Updating Kernel and Logging Messages

The Kernel controls everything on the Linux system. `systemd` is the default system inititialization scheme in RHEL7 replacing init and Upstart.

The boot process on an x86 computer can be split into 4 main phases: Firmware phase, Boot loader phase, Kernel phase & Initialization phase

* The firmware is the BIOS or the UEFI code that is stored in flash memory on the x86 system board. The first thing that it does is run the power-on-self-test (POST) to detect, test and initialize the system hardware components
* When it discovers a boot device, it loads GRUB2 (GRand Unified Bootloader) into memory and passes control over to it
* GRUB2 is loaded into memory and takes control, it searches for the kernel in `/boot` file system
* The configuration can be seen in `/boot/grub2/grub.cfg`

#### Managing GRUB

* After the firmware phase the bootloader presents a menu with a list of bootable kernels. Pressing a key allows the autoboot process to be stopped to interact with GRUB
* The grub.cfg file shouldn't be manually edited instead the `grub2-mkconfig` tool is used along with `/etc/default/grub` file
* An error in `grub.cfg` can result in an unbootable system

```bash
  # configuration available in /etc/grub2.cfg which is a ln
  ls -ltr /etc/grub2.cfg
  # see the defined GRUB behavior
  /etc/default/grub
  # reproduce the grub.cfg file
  grub2-mkconfig -o /boot/grub2/grub.cfg
  grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
```

#### Using the GRUB 2 Command Line

We can enter the `grub>` command line by entering `c` when the menu is displayed:

```bash
# see the commands available
grub > help
# list all available partitions and logical volumes
grub> ls 
# load the LVM module
grun> insmod lvm
# make the root variable the device identified to contain the root
grub> set root=(lvm/rhel-hostmachine1-root)
# find the root volume and view the fstab to see which should contain the root volume
grub> cat (lvm/rhel_hostmachine1-root)/etc/fstab
# enter the linux command to specify the kernel and root directory. This can take some trial and error and using tab will help
grub> linux (hd1,gpt2)/vmlinuz-4.18.0-147.el8.x86_64 root=/dev/mapper/rhel_hostmachine1-root/
# specify the RAM disk command and file location to load into memory
grub> initrd (hd1,gpt2)/initramfs-4.18.0.147.el8.x86_64.img
# boot the machine
grub> boot
```

#### Reinstalling GRUB2

```bash
# see the config files and remove
rpm -qc grub2-tools
rm -rf /etc/grub.d/*
rm -f /etc/default/grub
# reinstall grub2
yum install grub2-tools
# attempt to re-created the grub.cfg file
grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
# 
```

#### Modify GRUB Boot Order

```bash
# see what GRUB is botting into
grubby --default-index
# see details
grubby --info=ALL
# set default kernel to 1
grubby --set-default-index=1
```

#### Resetting the Root User Password

```bash
  # we can update the password without seeing it
  pwmake 128 | passwd --stdin root
  # interact with GRUB as shown above
  # find boot string that starts with linux16 and append
  init=/sysroot/bin/sh
  # or append rd.break. This interrupts the boot sequence before the root filesystem is mounted
  rb.break
  # confirm presence of root filesystem
  ls /sysroot
  # enters emergency mode
  # remounte root file system in read/write mode
  mount -o remount,rw /sysroot
  # change root filesystem 
  chroot /sysroot
  # enter a new password
  passwd
  # create an empty hidden file called .autorelabel at the root of the directory tree to instruct the system to perform SELinux relabelling
  touch /.autorelabel
  exit
  reboot
```

#### Linux Kernel

```bash
  # determine the Kernel version
  uname -r
  # view currently loaded modules
  lsmod
  # see detail about a module
  modinfo kvm
  # see info about kernel
  cat /proc/version
  # see infomation about a moduke
  modinfo dm_mirror
```

#### init and Upstart

The init program is the first process that spawns in the userland at system boot. It serves as the root process for all processes. init was enhanced in UNIX System V with run levels. Upstart was introduced in RHEL6 but was replaced by systemd in RHEL7. 

#### systemd

`systemd` is short for system daemon. It improves system init and state transitioning by introducing parrallel processing of startup scripts, improved handling of service dependencies, and an on-demend activation of service daemons using sockets and D-Bus. 

systemd creates sockets for all enabled services that support sock-based activation instantaneously at the very beginning of the initialization process, and passes them to daemon processes as theh attempts to start in parrallel. 

* Socket is a communication method that allows a single process running on a system to talk to another processes on the same or remote system
* D-Bus is another communication method that allows mutliple services running in parallel on a system to talk to one another on the same or remote system

```bash
yum install psmisc
# see pstree
pstree -pu
```

There is backwards compatibility with old SysV runlevels as can be seen with the runlevel links: 

```bash
ls -l /usr/lib/systemd/system/runlevel?.target
# see dependencies of a unit
systemctl list-dependencies graphical.target
# see current targey
systemctl get-default
```

#### Units

Units are systemd objects that used for organizing boot and maintenance taks. systemctl is the primary command for interaction with systemd.

```bash
  # list all known units and their status
  systemctl
  # see units of type socket
  systemctl -t mount --all
  # see loaded and active targets
  systemctl -t target
```

Targets are logical collection of units. They are a special systemd unit type with the .target file extension. 

```bash 
  # list all service units
  systemctl list-units --type=service --all
  # list all units of type socket
  systemctl list-sockets
  # lit unit files
  systemctl list-unit-files
  # check the status of atd
  systemctl status atd
  # list dependencies
  systemctl list-dependencies atd
  # show details for the atd service
  systemctl show atd
  # view time spent by each task during the boot process
  systemd-analyze blame
```

#### Switch Between Targets

```bash
# swith to mutli-user target
systemctl isolate mulit-user.target
# switch to the graphical target
systemctl isolate graphical.target
# display time required to boot the system
systemd-analye time
# display detailed breakdown by service
systemd-analyze blame
```

Boot into a different target:

```bash
# reboot the system and access the GRUB menu by pressing e
# edit the line to remove rhgb quiet and add:
systemd.unit=multi-user.taget
# repeat for rescue target
systemd.unit=rescue.taget
# repeat for emergency target
systemd.unit=emergency.targe
# repeat for rd.break
rd.break
```

#### System Logging

System logging (syslog) captures messages generated by the kernel, daemons, commands etc. The daemon responsible is called rsyslogd. 