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

1. A user contacts the AS for initial auth via the `kinit` command
2. The AS asks for the user's password, validates it, and generates a TGT for the user. It also produces a session key.
3. The AS returns the credentials (TGT plus the session key) to the user which the user decrypts by entering their password. The TGT has a limited validity and is set to expire  aftet a few hours
4. The user sends the TGT and session key  to the TGS asking to grant the desired access. The TGS verifies  the user's credentials by decrypting the TGT, and assembles a service ticket for the desired service and encrypts it with the service host's secret key.
5. It transmits the service ticket to the user along with the session key. 
6. The user stores the service ticket with its secrey key and validates the user's identity and the authorization to access the service. 

