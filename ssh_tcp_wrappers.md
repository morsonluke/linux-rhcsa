# Securing Access with SSH and TCP Wrappers

*Secure Shell* is a network protocol that delivers a secure mechanism for data transmission. The secure shell has a set of utilities for logging in, tranferring files and executing commands securley. 

*TCP wrappers* is a host-based service that is used to control access to network services running on the system. 

OpenSSH is a free, open source implementation of proprietry SSH. OpenSSH v2 supports the following algorithms:
* RSA (Rivest-Shamir-Adleman)
* DSA (Digital Signature Algorithm)
* ECDSA (Elliptic Curve Digital Signature Algorithm)

#### Encryption Technique

|  Encryption | Description | 
| --- | --- | 
| Symmetric Encryption   | Uses a single secret key (or a pair) to protect authentication traffic | 
|  Asymmetric Encryption | Uses private/public key combination for encryption. The client transmute the information using a public key and the server decryts using the private key | 

Once an encrypted channel is established, other negotiations take place. 

|  Authentication | Description | 
| --- | --- | 
|  GSSAPI-Based | GSS API allows security mechanisms such as Kerberos to be used. With this method an exchange of tokens take place between client and server to validate user identity  | 
|  Host-Based | | 
| Private/Public Key-Based | |
| Challenge-Response| |
| Password-Based | | 

```
# view configuration files
cat /etc/ssh/sshd_config
cat /etc/ssh/ssh_config
```

#### TCP Wrappers

TCP Wrappers is a host-based mechanism that is used to limit access to wrappers-aware TCP services on the system by inbound clients.

When a TCP client request comes in, the wrappers daemon `tcpd` scans the `.allow` file and then the `.deny` file.