# Managing Users and Groups

RHEL supports three fundamental user account types: root, normal and service.

User account information for local users is stored in the `/etc` directory in `passwd, shadow, group and gshadow`.

```
# check for inconsistencies in passwd and shadow files
pwck
```

#### Users

```
# see default values for useradd command
useradd -Dte
# add a test user
useradd testuser sh 
# see what has been added to the different files 
cd /etc; grep testuser passwd shadow group gshadow
```

Create a user with no login access

```
# create a user with no login access  nm us
useradd -s /sbin/nologin user4 
# assign a passwod
echo user123 | passwd --stdin user4
# check contents of passwd file
cd /etc ; grep user4 passwd
# add password again for another user
passwd -n 7 -x 28 -w 5 testuser
```

#### Groups

```
# add a group
groupadd -g 5000 linuxadmins
# addl user1 to linuxadmins
usermod -a -G linuxadmins user1
# see groups
groups user1
```

#### Shell Startup Files

* `etc/bashrc` defines functions and aliases etc. Includes settings from the shell scripts in /etc/profile.d
* `etc/profile` sets common env variables for users and startup programs. 
* `etc/profile.d` contains scripts for bash and C shell users that are executed by /etc/profile file. 

Per user shell start up files override  or modify system defaults

* `~/.bashrc` defines functions and aliases.  
* `~/.bash_profile` sets environmental variables. 
