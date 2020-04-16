#### SSH Tunneling

```bash
# local tunnel
ssh -L 8080:Server2:80 user@Server1
# remote tunnel 
ssh -R 8080:localhost:80 user@Server1
# dynamic tunnel 
ssh -D 8080 user@server1
```