# Firewalld and Kerberos

### Firewalld

Firewalld is a way of interacting with iptable rules. 

### Kerberos

Kerberos is a client/server auth protocol that works on the basis of digital tickets to allow systems to prove their identity to each other.

The Kerberos auth mechanism surrounds a central  administration server.

```
# see port and protocol information
grep -i kerberos /etc/services
```

| Terminology | Description | 
| --- | --- |
| Authentication Service (AS) | Service that runs on the Key Distribution Center (KDC) server to authenticate clients and issue initial tickets |
| Key Distrtibution Center (KDC) Database) | A database of principlas and their corresponding encryption keys |
|  KDC Server  |  A central server, that runs on the AS and the Ticket Granting Service (TGS) |
| Principlal  | A verified client that is recorded in the KDC database and  to which the KDC can assign tickets |
| Realm  | The administrative territory of a KDC   |
| Service Ticket | An encryted digital certificate to authenticate a user to a specific network service. It is issued by the TGS after validating a user's TGT and it contains a session key, principlan name, expiration time etc. |
| Ticket Granting Service (TGS) | A service that runs on the KDC to generate and issue service tickets to clients |
| Ticket Granting Ticket (TGT) | An initial encrypted digital certificate that is used to identify a client to the TGS at the time of requesting service tickets. |

