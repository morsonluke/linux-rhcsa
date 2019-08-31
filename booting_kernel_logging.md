# Booting, Updating Kernel and Logging Messages

The boot process on an x86 computer can be split into 4 main phases:
    * Firmware phase
    * Boot loader phase
    * Kernel phase
    * Initialization phase


```
# determine the Kernel version
uname -r
--> 3.10.0-957.27.2.el7.x86_64
# view currently loaded modules
lsmod
# see detail about a module
modinfo kvm
```

#### systemctl

systemctl is the primary command for interaction with systemd.

```
# list all known units and their status
systemctl
```