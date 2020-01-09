## Files and File Permissions

* RHEL follows the File system Hierarchy Standard (FHS)
* The `/boot` file system contains the Linux kernel, boot support files and boot configuration files
* The `/var` contains data that frequently changes while the system is operational
* `/usr/lib` contains shared library routines requires by commands/programs in /usr/bin and /user/sbin and kernel and other programs
* `usr/sbin` contains system admin commands not intended for execution by regular users.
* The `/dev` file system contains device nodes for physical hardware and virtual devices. These device nodes are created and deleted by the udevd service as necessary. There are two types of device files: character (or raw) and block. Character ddevices are access serially while block devices parallely
* `/proc` maintains the information about the current state of the Kernel. The contents are created in memory at boot time and a number of system utilities such as `top, ps etc.` are actually referencing these files 

```
# see the output of the files from /proc
cat /proc/cpuinfo
cat /proc/meminfo
```

#### Paths

```
# see absolute path in relation to /
pwd
# see the type of file
file .bash_profile
# sybmolic link are shortcuts to another file or directory
ll /usr/sbin/vigrlrwxrwxrwx. 1 root root 4 Aug  8 12:39 /usr/sbin/vigr -> vipw
```

#### Create Files and Directories

```
# create a file using touch
touch newfile
# create a file using cat, add text and Cltr+d
cat > newfile
# create using vi
vi newfile
# create a directory 
mkdir newdir
```

#### Listing Files and Directories

The ll command list files and dictories

```
-rw-rw-r--. 1 guser guser 5 Aug 28 12:19 newfile
```

| Column | Description |
| ---    |  ---         |
| -rw-rw-r--. | The first character tells the file type and the next 9 show permissions |
| 1 |  Number of links |
| guser | Owner name | 
| guser | Owner group name | 
|  5 | File size in bytes |
| 5 Aug 28 12:19 | Date n' that |
| newfile | file name | 

#### Displaying Contents of Files

```
# display contents using cat
cat .bash_profile
# use more to and less to view long text fiels
more /etc/profile
less /etc/profile
# see the first 100 lines of a file
head -100 /etc/profile
# view a log file while it's being updated
tail -f /var/log/messages
# send the data from a log file in progress
cat /dev/null > nifi-bootstrap_2019-10-02.log
# list which processes are using which files
lsof +L1
```

#### Copying and moving files

```
# copy file1 to file2 in the same directory
cp file1 file2
# copy a directory using the -r flag
cp -r dir1 dir2
# move a file. The -i option requires user confirmation
mv -i file1 file2
# remove file
rm -i file
# remove directory
rm -r file
# remove an empty directory
rmdir test3
```

#### Control Attributes

There are two commands lsattr and chattr that are used for attribute management.

| Attribute | Description |
| ---    |  ---         |
| a | can only be appended |
| A | prevents updating the access time |
| c | automatically compressed | 
| D | changes on directory are written synchronously to disk | 
| e | file uses extents for mapping the block on disk |
| i | file cannot be changed, renamed or deleted |
| S | changes in a file are written synchronously to disk | 

```
# see the current attributes for a file
lsattr file1
# update attribues of a file
chattr +a file1
# try to copy content of another file to it
cat /etc/fstab > file1
# append operation will work while above will not
cat /etc/fstab >> file1
# remove attributes
chattr -ia file1
```

#### Finding Files

The find command is very powerful as it can recursively search the directory tree and perform actions on any files find if required.

It takes the form `find path search option action`

```
# find a file in the home directory
find . -name -filetofind -print
# find files large than 100MB in /proc directory
find /proc -size +100M
# search for all the files in the system with sticky bit
find / -type d -perm -1000
# find all files called tmp in the entire directory and ask for removal confirmation 
find / -name core -ok rm {} \;
# find files modified in the last 10 days and show file type
find / -type f -mtime -10 -exec file {} \;
```

#### Linking Files/Directories

Each file has associated metadata which is called the file's *inode*. The inode is assigned a unique number that is used by the kernel for managing the file. 

A soft link or symlink allows one file to be associated with another.

Hard links associate files with a single inode number making all files undistinguishable.

```
# create a soft link for the newfile in the home directory
cd ~
ln -s newfile newfilelinked
# soft links can be seen in the / directory they begin with the letter l and have an arrow pointing tothe source file
ll /
--> lrwxrwxrwx.  1 root root    7 Aug  8 12:38 bin -> usr/bin
# create a hard link
ln newfile2 newfile3
```

The `ll` command shows the following output `lrwxrwxrwx`

| Characters | Description |
| ---    |  ---         |
| l | The first character tells the file type which in this case is a symbolic link |
| rwx | First three characters show the permissions for the owner  |
| rwx | Shows the permissions for the owner's group | 
| rwx | Shows permissions for public | 

#### Permissions

Permission classes are user, group and public. Permission types are read, write and execute. Permission modes are add, revoke and assign.

| Permission Class | Description |
| ---    |  ---         |
| User (u) | The owner of the file or directory |
| Group (g) | A set of users that have identical access on files and directories that they share |
| Others (o) | All others users - public | 

`chmod` can modify permissions using either **symbolic** or **octal** notation. In the three 3-digit octl numbering we have X-X-X with the corresponding weights  4-2-1.

```bash
# add execute permission for the owner
chmod u+x newfile -v
# add write permission for group members and public
chmod go+w newfile
# remove write permissions for the public
chmod o-w newilfe
# assign rwx for all u,g,o using octal notation
chmod umnewfile
# allow specific access
chmod u=rw,g=rwx,o=--- /tmp/project/
```

Linux assigns default permissions to a file/directory at the time of its creation. These are calculated from the umask permission value substracted from a preset value called initial permissions. In RHEL the default umask is 0022 for the root and 0002 for all regular users. 

```bash
# see the umask value
umask
# change ownership of file to newuser
chown newuser newfile
# change group ownership
chgrp newgroup newfile -v
# assign ownership and owning group at the same time
chown newuser:newgroup newfile
# change ownership recursively 
chown -R user100:user100 dir
```

We can see how the umask is defined in `/etc/profile`:

```bash
if [ $UID -gt 199 ] && [ "`/usr/bin/id -gn`" = "`/usr/bin/id -un`" ]; then
    umask 002
else
    umask 022
fi
```

`umask` cannot be configured to allow automatic creation of executable files so the:

| umask | Created files |
| --- | --- | 
| 000 | 666 (rw-rw-rw-) | 
| 002 | 664 (rw-rw-r--) |
| 022 | 644 (rw-r--r--) |

#### Special Permissions

| Special Permission | On an Executable | On a Directory |
| --- | --- | --- |
| SUID | When the file is executed, the effective user ID of the process is that of the file | No effect | 
| SGID | When the file is executed, the effective group ID of the processes is that of the file | Gives files created in the directory of the same group ownership as that of the directory |
| Sticky bit | No effect | Files in the directory can be renamed of removed only by their owners |

setuid is set on executable files at the owner level. With this bit set, the file is executed by other regular users with the same priviledges at that of the owner. 

```bash
ll /usr/bin/su
--> -rwsr-xr-x. 1 root root 32208 Mar 14 10:37 /usr/bin/su
# remove the setuid
chmod u-s /usr/bin/su
# set it back 
chmod 4755 /usr/bin/su
```

setgid is the same concept but at the group level.

```bash
  # see an example with setgid
  ll /usr/bin/wall
  # remove setgid
  chmod g-s /usr/bin/wall
  # put it back
  chmod 2555 /usr/bin/wall
```

Use setgid for Group collaboration

```bash
  # add group sdatagrp 
  groupadd -g 9999 sdatagrp
  # add users
  usermod -G sdatagrp user100
  usermod -G sdatagrp user200
  # create directory
  mkdir /sdata
  # set ownership 
  chown root:sdatagrp /sdata -v
  # set gid
  chmod g+s /sdata -v
  # verify 
  ll -d /sdata
```

The sticky bit is set on public writable directories. This protects file and sub-directories being deleted by other regular users. 

```bash
  ll -d /tmp
  # The bolded t in
  drwxrwxrwt. 8 root root 172 Aug 28 08:18 /tmp
  # set the sticky bit
  chmod 1755 /var -v
  # unset
  chmod 755 /var
  # see files with this set
  find / -type d -perm -1000
```