# File Storage with Samba

Samba is a networking protocol that allows Linux and UUNIX to share file and print resources with Windows and other Linux/UNIX systems.

RHEL7 includes the support for Samba software v4.1, which uses the SMB3 protocol that allows encrypted transport connections to other systems. 

*Samba Daemon* - Samba/CIFS are client/server protocols that use the smbd daemon on the server to share and manage directories and file systems. This uses TCP port 445 and is also responsible for share locking and user auth. 