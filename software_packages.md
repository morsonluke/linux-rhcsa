# Managing Software Packages

RHEL is essentially a set of RPM packages slung together to form an OS. It's all built around the Linux Kernel and includes thousands of packages that are "signed, sealed, delivered".

A software packages is a group of files organised in a directory structure along with some metadata. There's often package dependency where one package relies on another which can at times cause a toxic relationship. 

```
# see all packages installed
rpm -qa
# list all files in a package
rpm -ql iproutei
# identify which package the file is associate with 
rpm -qf /etc/passwd
# show information about a package
rpm -qi setup
# list all dependencies for a specified package
rpm -qR iptablesp
```

```
# view GPG public ket
rpm -q gpg-pubkey
```

#### yum

The `yum` command is the front-end to the rpm  command. A yum repository is a digital library for storing software packages. 

```
# see the key config file for yum
cat /etc/yum.conf
# see additional config
yum-config-manager
# see all enable  repos accesible to the system
yum repolist
# list all packages that begin with with a string
yum list installed string*
```