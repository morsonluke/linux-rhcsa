# Booting, Updating Kernel and Logging Messages

The Kernel controls everything on the Linux system. `systemd` is the default system inititialization scheme in RHEL7 replacing init and Upstart.

The boot process on an x86 computer can be split into 4 main phases:
    * Firmware phase
    * Boot loader phase
    * Kernel phase
    * Initialization phase

* The firmware is the BIOS or the UEFI code that is stored in flash memory on the x86 system board. The first thing that it does is run the power-on-self-test (POST) to detect, test and initialize the system hardware componenets
* When it discovers a boot device, it loads GRUB2 into memory and passes control over to it
* GRUB2 is loaded into memory and takes control, it search for the kernel in `/boot` file system. 
* The configuration can be seen in `/boot/grub2/grub.cfg`. The GRUB behaviour at boot time is defined in `/etc/default/grub`

#### Linux Kernel


```
# determine the Kernel version
uname -r
--> 3.10.0-957.27.2.el7.x86_64
# view currently loaded modules
lsmod
# see detail about a module
modinfo kvm
# see info about kernel
cat /proc/version
# see infomation about a moduke
modinfo dm_mirror
```

#### systemctl

systemctl is the primary command for interaction with systemd.

```
# list all known units and their status
systemctl
```