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

#### Setting up a Digital Ocean Droplet

One option is to set up a cheap cloud Linux machine (CentOS 7) for testing. I did this on Digital Ocean as I documented [here](https://morsonluke.github.io/projects/droplet/).

#### Using Virtualbox

A few key points not a full guide. This allows for a GUI installation which is needed for elements of the exam.

1. Download Virtualbox
2. Download CentOS Image
3. Attach the OS ISO image as a disk
4. The Installation Summary screen appears
5. Choose Software Selection -> `Server with GUI`
6. Configure IP and Hostname 
7. Add a root password and a users
8. Confirm the post installation tasks

#### Creating automated labs

* [Creating the labs Using Vagrant](https://github.com/AnwarYagoub/RHCSA-RHCE-Lab-Environment)