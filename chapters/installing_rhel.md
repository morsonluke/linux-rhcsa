### Linux

Red Hat, Inc. released their OS Red Hat Linux (RHL) under the GNU GPL in 1994. There are two 100% rebuilds of RHEL. These are CentOS and Scientific Linux. 

* The initial realse of RHEL7 is based on kernel version 3.10
* The RHEL7 installation program is called Anaconda
* Anaconda provides three main interfaces: graphical, text-based and kickstart
* Text-based is used on systems with no graphics cards where kickstart is fully-automated method that could be used to load a number of systems at the same time 
* RHEL7 is supported with 64-bit Intel, AMD, IBM Power7 andIBM System zEnterprise processors
* There are six text-based virtual console screens for monitoring the installation (accessible via `Ctrl+Alt+F[1:6]`)

```
# to enable the GUI on a system 
yum group install "X Window System" "GNOME" -y
systemctl set-default graphical.target
sudo shutdown -r now
```

#### RHEL-8 Server Installation

RHEL can be deployed various ways:
* Installing on physical hardware
* Installing on virtual machine
* Red Hat Enterprise Linux as a container
* Red Hat Enterprise Linux as a cloud instance

#### Using VMware

1. Download VMware Fusion for mac and choose the free license for 30 days option
2. Download the CentOS8 image from Centos
3. Create a new image. Ensure VMware is able to run in System Preferences -> Security
4. The software selection can be 'Minimal Install'
5. Accept 'Installation Destination' defaults and 'Begin Installation'
6. Add the root password along with a user

#### Install GNOME Desktop on Minimal Server from DVD 

```bash
# login as root
su -
# create a directory
mkdir /dvdinstall
# check which device has the DVD
lsblk
# mount drive (dirve must be formatting usnig parted/fdisk)
mount /dev/sr0 /dvdinstall
# check 
df -h
# go to the repo defintion and create a file
cd /etc/yum.repos.d
vi dvdinstall.repo
```

Add the following:

```
[BaseOS]
name=BaseOS
baseurl=file:///{location}
gpgcheck=0
enable=1

[AppStream]
name=AppStream
baseurl=file:///{location}
gpgcheck=0
enable=1
```

In this case I had to remove other repos to make this work first:

```bash
  mv CentOS-AppStream/repo /tmp/
  mv CentOS-Base.repo /tmp/
  yum clean all
  yum repolist
  yum -y groupinstall "Server with GUI"
  # set default boot to be graphic
  systemctl set-default graphical-target
  reboot
```

#### Setting up a Digital Ocean Droplet

One option is to set up a cheap cloud Linux machine (CentOS 7) for testing. I did this on Digital Ocean as I documented [here](https://morsonluke.github.io/projects/droplet/).

#### Using Virtualbox

A few key points and not a full guide. This allows for a GUI installation which is needed for elements of the exam.

1. Download Virtualbox
2. Download CentOS Image
3. Create a New virtual machine and accept the default options
3. Attach the OS ISO image as a disk (Settings -> Storage -> Add Optic Drive)
4. The Installation Summary screen appears
5. Choose Software Selection -> `Server with GUI`
6. Configure IP and Hostname 
7. Add a root password and a users
8. Confirm the post installation tasks

#### Creating automated labs

Using VirtualBox is buggy and slow. An easier way using just the CLI:

* [Creating the labs Using Vagrant](https://github.com/AnwarYagoub/RHCSA-RHCE-Lab-Environment)