## Basic Linux Tools

#### SSH

We can ssh from another Linux system with the hostname or IP address with either of:

```bash
ssh -l user1 {ip address}
ssh user1@{ip address}
```

#### Documentation

We can see the usage of a command in a number of ways:

```bash
# see the man pages for a command 
man mkdir
# search the installed manual pages 
man -k password
# if new packages are install update the man pages using
mandb
# see a short description of the specific command
whatis ls
# search man pages and key words
apropos log
```

The `/usr/share/doc` directory stores documentation for all installed packages under sub-directories. This has a lot of information about the packages.

```bash
ll -d /usr/share/doc/sudo*
```

#### Directories

```bash
# list files and directories
ls
# ll is an alias of ls -l
type ll
#ll is aliased to `ls -l --color=auto'
# shows the working directory
pwd
# change directory
cd 
# make a directory
mkdir -v new1
# go up a directory to the parent
cd ..
```

#### Users

```bash
# Display the terminal name we are logged on to
tty
# list the users currently logged into the system
who
# list the information for only the user running the command
who am i
# the what command gives more information than the who command
w
# show the systems current time and low long it has been up for
uptime
# name of the real use who logged into the system
logname
# display user and gruop name information for the user
id
# list all the groups a user is a member of 
groups
# view successful login attemps
last
# view history of failed user login attemps
lastb
# see recent user logings
lastlog
```

#### System Information

```bash
# see basic info about the system
uname
# view the hostname 
hostnamectl
# change hostname
hostnamectl set-hostname host1.example.com 
# display and set system date and time
timedatectl
timedatectl set-timezone Europe/Dublin
# set the date
date --set "2019-11-01 12:00:00"
# see the path the command will execute if run with absolute path
which cat
# count number lines, words and characters
wc /etc/profile
# see information about PCI buses and devices connected
lspci -m 
# see information about USB buses and devices connected
lsusb
# see info about the processor
lscpu
# see the calendar
cal 2020
```

#### Compression Tools

Compression tools are used to save space

```bash
# gzip creates a compressed file of each of the files
gzip /root/anaconda-ks.cfg
# unzip the file
gunzip /root/anaconda-ks.cfg 
# bzip2 & bunzip2 can also be used
```

Archiving tools include tar and star which have the ability to preserve general file attributes (ownership, group membership)

```bash
# Create a tarball of the entire /home directory
tar cvf /tmp/home.tar /home
# to restore /home from home.tar
tar xvf /tmp/home.tar
# copy mutiple files
tar cvf /tmp/files.tar file1 file2
# see contents of tar
tar tvf files.tar
# restore the files
tar xvf files.tar
# make a tar and compress with bzip2
tar cvzf files.tar.gz /vagrant/
# also preserve selinux and attributes and compress with bzip2
tar cvj --selinux --xattrs -f /tmp/file.tar.bz2 /home
```

The star command is an enhanced version of tar. 

```bash
# create a tarball containing entire /etc directory with all extended file attributes and SELinux file context
star cvf /tmp/etc.tar -xattr -H=exustar /etc
```

#### vi

The vi editor is a text editing tool that allows you to create and modify text files. There's a multitude of options with vi to explore.

```bash
# create a new file using vi
vi new_file
# exit and save using 
esc + :wq
# write changes into a new file called file2
:w file2
# exit if modifications were made, but we do not wish to save them
:q!
```

#### Shutdown

```bash
# shutdown the system in 10 minutes and notify users
shutdown +10 {message}
# reboot the machine 
shutdown -r now
systemctl reboot
# ways to shutdown the machine
shutdown +0
init 0
systemctl halt
systemctl poweroff
```
