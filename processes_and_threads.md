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

"Processes within process" are called threads. Why? 
* By decomposing into multiple sequential threads that run in quasi-parallel it simplifies the programming model
* Threads are more lightweight and therefore faster
* They are more performany from an I/O and computing perspective

#### Interprocess Communication

Process need to communicate with each other. Race conditions occur when processes are reading or writing some shared data and the final result depends on who runs precisely when. The key is to ensure that any shared memory access in this critical region is not access at the same time. There are a number of abstraction for this. 