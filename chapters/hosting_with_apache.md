## Hosting Websites with Apache

The Apache HTTP server is a client/server application that relies on the `httpd` daemon running on the server to allow the transfer of web content. 

```bash
# see if installed
yum list installed | grep httpd
# otherwise install
yum install httpd
# see config
ll /etc/httpd
# see log files
ll /var/log/httpd
```

#### Defauly Apache Web Server

```bash
yum -y install httpd elinks
firewalld-cmd --permanent --add-service=http ; firewall-cmd --reload
systemctl enable httpd
systemctl start httpd
elinks http://localhost
```

#### Configure Apache as an installation server 

```bash
# mount the dvd
mount /dev/sr0 /media
# create the directory
mkdir /var/www/html/inst
# copy files from the mounted DVD
cp -a /media /var/www/html/inst
# ensure correct SELinux context
chcon -R --rerference=/var/www/html /var/www/html/inst
# open port 80
firewall-cmd --permananet --add-service=http
firewall-cmd --reload
# restart services
systemctl restart httpd
```

If we have issues it could be to do with SELinux. If we changed the default directory for example it may not have the correct permissions:

```bash
# check SELinux permissions
ls -lZ /var/web
# check what permissions it should have from default location 
ls -lZ /var/www/html
# see what packages we need to manage these
yum provides \*/semanagee
# install packag
yum -y install policycoreutils-python
# change context
semanage fcontext -a -t httpd_sys_content_t '/var/web(/.*)?'
# apply changes to filesystem
restorecon -R /var/web
```

#### SSL/TLS

Secure Socket Layer (SSL) is a cryptographic protocol that allows networked systems to communicate securely. Apache webservers that operate on top of SSL/TLS may be referred to as HTTPS or SSL web servers.

To obtain a CA, the applicant generates a private/public encryption key pair and a Certificate Signing Request (CSR) on the server for which the certificate is desired. The CSR is encoded before it is trasmitted to the CA. The CA reviews the CSR and issues a signed certificate. 

Another type of digital certificate is a self-signed certificate which is produced locally and mostly used for testing purposes. 

```
# see subcommands
openssl list-standard-commands
openssl list-cipher-commands
openssl list-message-digest-commands
```

#### Generate a Key Pair and Self-Signed Certificate

```
yum -y install mod_ssl openssl
cd /etc/pki/tls/certs
# generate private key
openssl genpkey -algorithm rsa -pkeyopt rsa_keygen_bits:2048 -out server1.example.com.key
# create a certificate sigining request
openssl req -new -key server1.example.com.key -out server1.example.com.csr
# self-sign 
openssl x509 -req -days 120 -signkey server1.example.com.key -in server1.example.com.csr -out server1.example.com.crt
# change permissions and store in private directory
chmod 0600 server1.example.com.key ; mv server1.example.com.key ../private
```

#### Common Gatweway Interface

CGI presents a standard way of generating dynamic content on websites using programs referred to as CGI scripts. 