# Bash Shell, Processes and Scheduling

```
# define a local variable
VAR1=centos7
# show the value
echo $VAR1
# unset the value
unset VAR1
```

The default locations for input, output and error are stdin, sdout and stderr. The locations can be < operator for stdin and > for stdout and stderr or using file descriptors of 0, 1 and 2 for stdin, stdout and stderr.

```
# redirect output of ll to ll.out
ll > ll.out
# to append to the output 
ll >> ll.out
```

```
# see the history 
history 20
# execute the command by its line number
!38
# repeat the last command
!!
```

#### grep

Linux has grep (global regular expression print) for when you fancy a bit of pattern matching. 

```
# search for a pattern for the user in the file
grep guser /etc/passwd
# exclude lines that contains the pattern nologin
grep -nv nologin /etc/passwd
# ^ marks the beginnings of a line or a word
grep ^root /etc/passwd
# $ marks the end of a line or word
grep bash$ /etc/passwd 
# print all lines from ll that contain cron or qemu
ll /etc | grep -E 'cron|qemu'
```

#### Metacharacters

```
# list names of all files in /etc director that begin with t
ls /etc/t*
# list all directories under /var/log with three characters in their name
ls -d /var/log/???
# list all directory names that begin with any letter between a and d
ls -d /etc/systemd/system/[m-p]*
# the | character sends the output of one command as input to the next
ll /etc | more
```

There are three quoting mechanisms that disbable their special meanings which are `\, '', ""`.

#### Processes

| `ps -ef` column | Description |
| ---    |  ---         |
| UID  | User ID or name of the process owner |
| PID  | Process ID of the process |
| PPID  | Process ID of the parent process |
| C   | Processor utilization for the process |
| TTY | The terminal on which the process started. ? represents a background process |q

```
# see statistics in real time 
top
# find the pid of cron
pidof crond
pgrep crond
# list all processes ownder by root
ps -U root
```

A process is spawned at a certain priority established by a numerical value called niceness. These go from -20 to + 19. 

The five process states are running, sleeping, waiting, stopped and zombie.

##### Scheduling

```
# we can see all atd and cron jobs 
cat /var/log/cron
# run a script in a few hours
at -f ~/.executablescript.sh now + 2 hours
```
