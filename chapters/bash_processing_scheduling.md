# Bash Shell, Processes and Scheduling

* The Shell interfaces users with the kernel by enabling requests for processes to be submitted
* A local variable is private to the shell in which it is created and its value cannot be used by processes that are not started in that shell
* The value of an environmental variable is passed from the current shell to the sub-shell during the execution of a script. 

```bash
  # set the value of a token to be used later
  TOKEN=$(curl http://127.0.0.1:10080/login -u user | jq -r '.token')
  # a variable is a temporary storage of data in memory
  VAR1=centos7
  # show the value
  echo $VAR1
  # make an environmental variable 
  export VR1
  # unset the value
  unset VAR1
```

The primary command prompt for the root user is # and a regular user gets a $. We can customise the primary command prompt. 

```bash
  export PS1="<$LOGNAME@`hostname`:\$PWD>"
```

The default locations for input, output and error are stdin, sdout and stderr. The locations can be < operator for stdin and > for stdout and stderr or using file descriptors of 0, 1 and 2 for stdin, stdout and stderr.

```bash
  # have the cat command output to standard out
  cat < /etc/cron.allow
  # redirect output of ll to ll.out
  ll > ll.out
  # to append to the output 
  ll >> ll.out
  # send error message to /dev/null
  find / -name core -print 2> /dev/null
  # send both to a file
  ls /usr /cdr &> outerr.out
  # redirect stdout to a file and then redirect stderr to stdout
  ll /usr /cdr > ~/dir.out 2>&1
  # send outpout to input of another command
  head /proc/cpuinfo | tr a-z A-Z
  # channel a file to a program's standard input
  head < /proc/cpuinfo
  # redirect output from dmesg to less 
```

```bash
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

RHEL 7 had four command-line shells: bash, ksh, tcsh & zsh. We can change the default shell: 

```bash
  # modify default shell in /etc/password for a user
  ksh_user:x:1002:1002::/home/ksh_user:/bin/ksh
```

#### grep

Linux has grep (global regular expression print) for when you fancy a bit of pattern matching. 

```bash
  # grep follows the basic patter
  grep [OPTIONS] PATTERN [FILE...]
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
  # see all lines that aren't blank or contain a comment
  grep -v '^$' /etc/nsswitch.conf | grep -v '^#'
```

#### Metacharacters

```bash
  # list names of all files in /etc director that begin with t
  ls /etc/t*
  # list all directories under /var/log with three characters in their name
  ls -d /var/log/???
  # list all directory names that begin with any letter between m and p
  ls -d /etc/systemd/system/[m-p]*
  # the | character sends the output of one command as input to the next 
  ll /etc | more
```

There are three quoting mechanisms that disbable their special meanings which are `\, '', ""`.

| Metacharacter | Description |
| --- | --- |
| `.` | Any single character |
| `[]`| Match any single character included within the [] e.g. `grep 'v[abc]grant /etc/passwd` |
| `?` |  Match the preceding element zero or one time |
| `+` |  Match the preceding element one or more times |
| `*` |  Match the preceding element zero or mote times `grep 'jo[a-z]*n' /etc/passwd` |
| `^` |  Match the beginning of a line |
| `$` |  Match the end of a line |

#### Processes

| `ps -ef` column | Description |
| ---    |  ---         |
| UID  | User ID or name of the process owner |
| PID  | Process ID of the process |
| PPID  | Process ID of the parent process |
| C   | Processor utilization for the process |
| TTY | The terminal on which the process started. ? represents a background process |

```bash
  # see statistics in real time 
  top
  # sort processes by memory in top
  shift + m
  # renice a process in top
  # press r and enter process id followed by niceness value
  # kill process by precessing k followed by the process id
```

```bash
  # find the pid of cron
  pidof crond
  pgrep crond
  # list all processes ownder by root
  ps -U root
```

A process is spawned at a certain priority established by a numerical value called niceness. These go from -20 to +19. 

```bash
  # see niceness values
  ps -efl 
  # see default nice value
  nice
  # set niceness value for top
  nice -2 top
  # modify a currently running process
  renice 5 1919
```

```bash
  # install httpd
  yum install httpd
  # ensure it is stopped
  systemctl stop httpd
  # start with a nice value of 20
  nice -n -20 httpd
  # see nice level 
  ps axo pid,comm,nice --sort=nice | grep httpd
  # renice
  renice -n 0 $(pgrep httpd)
```

The five process states are running, sleeping, waiting, stopped and zombie.

##### Scheduling

Job scheduling is a feature that allows a user to submit a command or program for execution at a certain time. 

Job scheduling and execution is taken care of by two daemons: atd and crond. While atd manges the jobs scheduled to run on etime in the future, crond is responsible for running job repetitively. At startup it reads schedules in files in `/var/spool/cron` and `/etc/cron.d` directories and slams them into memory for later execution. 

User access is controlled in the /etc directory in allow/deny files.

```bash
# add crontab enteries to a file
crontab file
# we can see all atd and cron activities 
cat /var/log/cron
# run a script in a few hours
at -f ~/.executablescript.sh now + 2 hours
```

Submit an at job:

```bash
  at 11:30pm 1/6/20
  at> find / -name core -exec rm {} \; & /tmp/core.out
  # view list of jobs
  ll /var/spool/at
  # or view by name of job ID
  at -c 1
  # list jobs
  at -l 
  # remove job
  atrm 1
```

Add a command to issue a statement to the system log

```bash
  at now +2 minutes
  >at logger "The system uptime is $(uptime)"
  # view the logs
  tail -f /var/log/messages
  # or 
  journalctl -f 
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

# Will only run on odd days:
0 0 1-31/2 * * command

# Will only run on even days:
0 0 2-30/2 * * command
```

``` bash
  # edit crontab file and add something
  crontab -e
  # view the crontab for a user
  crontab -l
  # remove
  crontab -r
```