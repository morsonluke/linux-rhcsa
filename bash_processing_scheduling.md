# Bash Shell, Processes and Scheduling

Shell interface users with the kernel by enabling requests for processes to be submitted. 

A local variable is private to the shell in which it is created and its value cannot be used by processes that are not started in that shell. The value of an environmental variable is passed from the current shell to the sub-shell during the execution of a script. 

```
# a variable is a temporary storage of data in memory
# define a local variable
VAR1=centos7
# show the value
echo $VAR1
# make an environmental variable 
export VR1
# unset the value
unset VAR1
```

The primary command prompt for the root user is # and a regular user gets a $. We can customise the primary command prompt. 

```
export PS1="<$LOGNAME@`hostname`:\$PWD>"
```

The default locations for input, output and error are stdin, sdout and stderr. The locations can be < operator for stdin and > for stdout and stderr or using file descriptors of 0, 1 and 2 for stdin, stdout and stderr.

```
# have the cat command output to standard out
cat < /etc/cron.allow
# redirect output of ll to ll.out
ll > ll.out
# to append to the output 
ll >> ll.out
# send  error message to /dev/null
find / -name core -print 2> /dev/null
# send both to a file
ls /usr /cdr &> outerr.out
```

```
# location of history file
echo $HISTFILE
# see the history 
history 20
# execute the command by its line number
!38
# repeat the last command
!!
# current directory
echo ~+
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

```
# see niceness values
ps -efl 
# see default nice value
nice
# set niceness value for top
nice -2 top
# modify a currently running process
renice 5 1919
```

The five process states are running, sleeping, waiting, stopped and zombie.

##### Scheduling

Job scheduling is a feature that allows a user to submit a command or program for execution at a certain time. 

Job scheduling and execution is taken care of by two daemons: atd and crond. While atd manges the jobs scheduled to run on etime in the future, crond is responsible for running job repetitively. At startup it reads schedules in files in `/var/spool/cron` and `/etc/cron.d` directories and slams them into memory for later execution. 

User access is controlled in the /etc directory in allow/deny files.

```
# we can see all atd and cron activities 
cat /var/log/cron
# run a script in a few hours
at -f ~/.executablescript.sh now + 2 hours
```

#### Crontab

The `/etc/crontab` file specifies the syntax that each cron job must comply with in order for crond to interpret and execute. 

```
# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
```


```
# view the crontab for a user
crontab -l
```
