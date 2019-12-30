# Sending and Receiving Electronic Mail

Simple Mail Transport Protocol (SMTP) is a networking protocol responsinle for email communication. It runs on top of the IP protocol and uses well-known TCP port 25 for its operation. 

An operational email has a heafty wedge of three letters abberviations to be understood:
* Mail User Agent (MUA)
* Mail Submission Agent(MSA)
* Mail Transport Agent (MTA) 
* Mail Delivery Agent (MDA)
* Post Office Protocol (POP)
* Internet Message Access Protocol (IMAP)

#### Postfix

```
# see config files
ll /etc/postfix
# the access file can be used to establish access controls
postmap /etc/postfix/access
# see default config not 
postconf -d
# see setting defined in the main.cf file
postconf -n
```