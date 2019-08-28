## Files and File Permissions

#### /proc

`/proc` maintains the information about the current state of the Kernel. The contents are created in memory at boot time and a number of system utilities such as `top, ps etc.` are actually referencing these files. 

```
# see the output of the files from /proc
cat /proc/cpuinfo
cat /proc/meminfo
```

#### Paths

```
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
