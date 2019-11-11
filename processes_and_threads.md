# Processes and threads

A central concepts in any OS is the process: an abstraction of a running program. A process is just an instance of an executing program, including the current values of the program counter, register and variables. 

A process is an activity of some kind. It has a program, input, output and state. In contrast, a program is something  that may be s tored on disk, not doing anything. 

Processes that run in the background are called daemons. 

Investigate what process is running on a port

```bash
    $ nmap localhost
    $ lsof -i :8042
    $ ps -ef | grep 21188
    $ netstat -tulpn | grep :50470
    $ pmap -x {process_id}
    $ pstree -p |  less
```