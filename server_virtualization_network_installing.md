# Server Virtualization and  Network Installing

Server virtualization allows a physical computer to host several virtual machines, each of which acts as a standalone computer running an OS.

Virtualization software deployed directly on bare-metal host machine is referred tto as the hypervisor software. KVM (Kernel-based Virtual Machine) is part of the Linux Kernel and comes as a native hypervisor with RHEL7. QEMU then uses the physical-to-virtual CPU mappings provided by KVM. libvirt is the virtualization management library.

```
# check if the processor supports virtualization
lscpu | grep -i virtualization
--> Virtualization:        VT-x
--> Virtualization type:   full
# check for flag vmx (intel processor) 
grep vmx /proc/cpuinfo
```
