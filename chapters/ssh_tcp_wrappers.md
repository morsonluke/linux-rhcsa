# Securing Access with SSH and TCP Wrappers

**Secure Shell** is a network protocol that delivers a secure mechanism for data transmission. The secure shell has a set of utilities for logging in, tranferring files and executing commands securely. 

**TCP wrappers** is a host-based service that is used to control access to network services running on the system. 

OpenSSH is a free, open source implementation of proprietry SSH. OpenSSH v2 supports the following algorithms:
* RSA (Rivest-Shamir-Adleman)
* DSA (Digital Signature Algorithm)
* ECDSA (Elliptic Curve Digital Signature Algorithm)

#### Encryption Technique

|  Encryption | Description | 
| --- | --- | 
| Symmetric Encryption   | Uses a single secret key (or a pair) to protect authentication traffic | 
|  Asymmetric Encryption | Uses private/public key combination for encryption. The client transmute the information using a public key and the server decrypts using the private key | 

Once an encrypted channel is established, other negotiations take place. 

|  Authentication | Description | 
| --- | --- | 
|  GSSAPI-Based | GSS API allows security mechanisms such as Kerberos to be used. With this method an exchange of tokens take place between client and server to validate user identity  | 
| Host-Based | Setup is done in `/etc/ssh/shots.equiv` file | 
| Private/Public Key-Based | This method uses a private/public key combination for user auth |
| Challenge-Response| Based on the responses to one or more arbitrary challange questions |
| Password-Based | Prompts the user to enter their password. Checks the password against the stored entry in the shadow (or passwd) file | 

``` bash
  # view configuration files
  cat /etc/ssh/sshd_config
  cat /etc/ssh/ssh_config
```

#### Commands 

| Command | Description | 
| --- | --- |
| scp |  secure alternative to `rcp`|
| sftp |   ... `ftp` |
| slogin |   ... `rlogin`  |
| ssh |  ... `telnet` and `rlogin` |
| ssh-add | Adds characteristics to ssh-agent |
| ssh-agent |  Auth agent. Holds private keys used |
| ssh-copy-id | Copies keys to remote systems  |
| ssh-keygen | Creates privarte and public keys |

Configure private/public key-based auth: 

```bash
  ssh-keygen
  # view the file
  cat ~/.ssh/id_rsa
  # copy file to server2
  ssh-copy-id -i ~/.ssh/id_rsa.pub server2
  # see login attemp (as root)
  tail /var/log/secure
  # execute a command using ssh
  ssh server /bin/cat ~/.ssh/authorized_keys
  # using sftp 
  sftp server2 
```

#### TCP Wrappers

TCP Wrappers is a host-based mechanism that is used to limit access to wrappers-aware TCP services on the system by inbound clients.

When a TCP client request comes in, the wrappers daemon `tcpd` scans the `hosts.allow` file and then the `hosts.deny` file.