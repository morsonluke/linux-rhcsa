## Basic Linux Tools

#### SSH

We can ssh from another Linux system with the hostname or IP address with either of:

```
ssh -l user1 {ip address}
ssh user1@{ip address}
```

### Common Linux Commands

This list a number of basic commands

#### Documentation

Before looking at the commands these are a few ways of finding out more information about those commands to know which options can be used.

```
# see the man pages for a command
man mkdir
# search the installed manual pages 
man -k password
# if new packages are install update the man pages using
mandb
# see a short description of the specific command
whatis ls
```

The `/usr/share/doc` directory stores documentation for all installed packages under sub-directories. This has a lot of information about the packages.

```
ll -d /usr/share/doc/sudo*
```

#### Directories

```
# list files and directories
ls
# ll is an alias of ls -l
type ll
--> ll is aliased to `ls -l --color=auto'
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

```
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

```
# see basic info about the system
uname
# view the hostname 
hostnamectl
# display and set system date and time
timedatectl
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
```

#### Compression Tools

```
# gzip creates a compressed file of each of the files
gzip /root/anaconda-ks.cfg
# unzip the file
gunzip /root/anaconda-ks.cfg 
# bzip2 & bunzip2 can also be used
# Create a tarball of the entire /home directory
tar cvf /tmp/home.tar /home
# to restore /home from home.tar
tar xvf /tmp/home.tar
```

#### vi

The vi editor is a text editing tool that allows you to create and modify text files. There's a multitude of options with vi to explore.

```
# create a new file using vi
vi new_file
# exit and save using 
esc + :wq
# exit if modifications were made, but we do not wish to save them
:q!
```