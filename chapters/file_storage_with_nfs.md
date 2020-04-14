# Sharing File Storage with NFS

The Network File System allows the sharing of file between systems over the network. It allows a directory or file systems on one system to be mounted and used remotely on another system. The remote system that makes it shares available for network access is referred to as an NFS server. 

NFS uses the Remote Procedure Call (RPC) and eXternal Data Representation (XDR) mechanism that allows a server and client to communicate with each other. 

| Daemon | Description | 
| --- | --- |
| `nfsd` | NFS server process responds to client request on TCP port 2049   |
| `rpcbind` |  Server/client - converts RPC program numbers into universal addresses   |
| `rpc_rquotad` |  Server/client - displays user quota information for remotely mounted shares   |
| `rpc_idmapd` |  Server/client - controls the mapping of UIDs and GIDs with corresponding username and groupnames based on config in /etc/idmapd.confile |

#### /etc/exports

The `/etc/exports` file defines the configuration for NFS shares. NFS security can be enhanced in a number of ways:
* A properly configured firewall
* TCP wrappers
* SELinux
* Kerberos authentication and encryption

#### Basic NFS Installation 

```bash
# install all packages including Samba, CIFS, and iSCSI
yum group install "File and Storage Server"
# we can just instill requirements for NFS
yum -y install nfs-utils
```

#### Export Shares to NFS Clients 

```bash
mkdir /common/nfsrhcsa
setsebool -P  nfs_export_all_ro=1 nfs_export_all_rw=1
# confirm settings 
getsebool -a | grep nfs_export
# add NFS service persistently to the firewalld config 
firewall-cmd --permanent --add-service nfs ; firewall-cmd --reload 
# set rpcbin and NFS to autostart
systemctl enable rpcbind nfs-server
systemctl start rpcbind nfs
systemctl status rpcbind nfs
# vi /etc/exports
/common    server2.example.com(rw,no_root_squash)
/nfsrhcsa  server2.example.com(sync)
```

```bash
# added this line to /etc/hosts
192.168.4.220   server2.example.local   server2
# export entries defined in /etc/exports file
exportfs -avrx
cat /var/lib/nfs/etab
```

#### Mount a Share on NFS Client 

```bash
yum -y install nfs-utils
mkdir /nfsrhcemnt
systemctl enable rpcbind
systemctl start rpcbind
systemcrl status rpcbind
vi /etc/fstb
server1.example.local:/common  /nfsrhcemnt nfs  _netdev,rw  0 0 
```

#### Setup a Samba Share and NFS Share

Server Message Block is a protocol that can be used to share access to files, printers and other resources. 

The name of the protocol has changed to cifs  (common internet filesystem), many packages still use the SMB terminology

```bash
# install Samba packages
yum install samba -y
# edit smb.conf
vi /etc/samba/smb.conf
```

Add the following:

```
[share]
       browsable = yes
       path = /smb
       writable = yes
```

```bash
# check an smb.conf configuration file for internal correctness
testparm
# create a user on the server
useradd shareuser
smbpasswd -a shareuser
# create the /smb path 
mkdir /smb & chmod 777 /smb
#  start the samba damon
systemctl start smb
# enable as all changes must survive a restart
systemctl enable smb
```

Setup the Samba Client on another instance

```bash
# The cifs-utils provides a means for mounting SMB/CIFS shares on a Linux system
yum install cifs-utils -y
# make mount poubt
mkdir /mnt/smb
```