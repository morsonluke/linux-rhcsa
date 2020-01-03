# Managing Software Packages

RHEL is essentially a set of RPM packages slung together to form an OS. It's all built around the Linux Kernel and includes thousands of packages that are "signed, sealed, delivered".

A software packages is a group of files organised in a directory structure along with some metadata. There's often package dependency where one package relies on another which can at times cause a toxic relationship. 

| `openssl-1.0.1e-34.el7.x86_64.rpm` | Componennt | 
| --- | --- |
| openssl | package name |
| 1.0.1e | package version |
| 34 | package release |
| el7 | stands for Enterprise Linux 7 |
| x86_64 | processor architecture. "noarch" would signify platform independent |
| .rpm | extension  |

Red Hat Subscription Manager (RHSM) isprovided by for subscription management and delivers teh software updates and support. Subscriptions for a single system can be managed by the Subscription Manager client application called `subcription-manager`. 

```bash
# metadata for installed package files is stored in /var/lib/rpm directory
# see all packages installed
rpm -qa
# see if mlocate is installed 
rpm -q mlocate
# list all files in a package
rpm -ql iproute
# list only the documentation files
rpm -qd mlocate
# list only the configuration files
rpm -qc mlocate
rpm -c mlocate --query
# identify which package the file is associate with 
rpm -qf /etc/passwd
# show information about a package
rpm -qi mlocate
# list all dependencies for a specified package
rpm -qR iptablesp
# see what package a file is associated with 
rpm -qf /etc/passwd
# verify the attributes
rpm -V dcraw
```

Installing packages

```bash
# install from dvd source 
rpm -ivh /mnt/Packages/zsh-html-5.5.1-6.el8.noarch.rpm
# remove a package
rpm -ev zsh
```

#### Vetifying Package Integrity


A package can be checked for integrity using the MD5 checksum and GPG public key. 

```bash
# import GPG keys
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
# check integrity 
rpm -K --nosignature /mnt/Packages/zsh-html-5.5.1-6.el8.noarch.rpm
# view GPG public key
rpm -q gpg-pubkey
```

#### yum

The `yum` command is the front-end to the rpm command. A yum repository is a digital library for storing software packages. 

```bash
# see the key config file for yum
cat /etc/yum.conf
# see additional config
yum-config-manager
# see all enabled repos accesible to the system
yum repolist
# list all packages that begin with with a string
yum list installed string*
# search for a package
yum search mlocate
```

#### Create a Local Yum Repository

```bash
# create a directory and hop into it
mkdir -p /var/local && cd /var/local
# copy a package into the current location
cp /mnt/Packages/dcraw* .
# install package for creating repo structure
yum -y install createrepo
# execute command to create the file structure 
createrepo -v /var/local
# define the local repo
vi /etc/yum.repos.d/local.repo
```

```yaml
[local]
name=local repo
baseurl=file:///var/local
enaled=1
gpgcheck=0
```

```bash
# clean the cache
yum clean all
# confirm repository has been created
yum -v repolist
```