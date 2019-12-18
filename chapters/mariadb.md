# Managing MariaDB

MariaDB is an open source database management system software that allows the creation and admin of databases. This requires a database management system (DBMS). 

 * The default log file for storing MariaDB logs is unsurprisingly in `/var/log/mariadb` 


#### Installing and Configuring MariaDB

```
yum -y install mariadb-server
systemctl enable mariadb
mysql_secure_installation
firewall-cmd --permanent --add-service mysql ; firewall-cmd --reload
systemctl start mariadb
systemctl status mariadb
# start the mysql shell
mysql -u root -p
```

#### Backing Up and Restoring

A backup  is a function of duplicating data to an alternative location for use in the event of a data loss. Restore is the opposite - it retrieves data from a backup location and puts it back to its original  place.