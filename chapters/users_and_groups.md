# Managing Users and Groups

RHEL supports three fundamental user account types: root, normal and service.

User account information for local users is stored in the `/etc` directory in `passwd, shadow, group and gshadow`.

```
# check for inconsistencies in passwd and shadow files
pwck
# check for same with group and gshadow files
grpck
# edit the password passwd and group files with a lock
vipw -s
vigr -s
```

| Command | Description | 
| ---     |             |
| pwconv, grpconv  | Create and updates the shadow/gshadow file and moves user passwords over from the passwd/group file |
| pwunconv, grpunconv | Moves user passwords back to the passwd/group file and removes the shadow/gshadow file | 

#### Users

```
# see default values for useradd command
useradd -Dte
# add a test user
useradd testuser sh 
# see what has been added to the different files 
cd /etc; grep testuser passwd shadow group gshadow
# modify users GECOS
usermod -c newvalue testuser
```

Create a user with no login access

```
# create a user with no login access
useradd -s /sbin/nologin user4 
# assign a passwod
echo user123 | passwd --stdin user4
# check contents of passwd file
cd /etc ; grep user4 passwd
# add password again for another user
passwd -n 7 -x 28 -w 5 testuser
```

Set up password aging on user accounts

```
# add password aging
passwd -n 7 -x 29 -w 4 student
# see changes
chage -l student
# modify using chage
chage -m 10 -M 30 -W 7 -E 2020-12-31 student
# prompt to change at next login, disable account expiry
chage -d 0 -m 5 -E -1 student
# delete user
userdel -r user4
```

```
# create a user with sudo access
sudo adduser tableau-admin
sudo passwd tableau-admin
# add user to wheel group
sudo usermod -aG wheel tableau-admin
# run command as user
su - c 'firewall-cmd --list-services'
```

#### Groups

```
# add a group
groupadd -g 5000 linuxadmins
# add another group sharing the GID of the same group
groupadd -o -g 5000 sales
# addl user1 to linuxadmins
usermod -a -G linuxadmins user1
# see groups
groups user1
```

#### Doing a bit of sudoing

The sudo utility is designed to provide protected access to administrative functions as defined in `/etc/sudoers` file. The file can be edited in a safe fashion using `visudo`. 

The file contains example of how access is defined such as for members of the wheel group. 

```
## Allows people in group wheel to run all commands
%wheel	ALL=(ALL)	ALL
```

#### Shell Startup Files

* `etc/bashrc` defines functions and aliases etc. Includes settings from the shell scripts in /etc/profile.d
* `etc/profile` sets common env variables for users and startup programs. 
* `etc/profile.d` contains scripts for bash and C shell users that are executed by /etc/profile file. 

Per user shell start up files override  or modify system defaults

* `~/.bashrc` defines functions and aliases.  
* `~/.bash_profile` sets environmental variables. 
