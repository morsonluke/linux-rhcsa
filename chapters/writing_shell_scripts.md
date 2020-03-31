# Writing Shell Scripts

Shell scripts are text files that contain Linux commands and control structures for the automation of tasks. 

An example shell script based on `/etc/cron.daily/logrotate` with some comments: 

```bash
# the first line of a shell script starts with a "sheband" `#! /bin/sh`. On RHEL this is symbolically linked to `/bin/bash`
#!/bin/sh
# rotate the logs
/usr/sbin/logrotate -s /var/lib/logrotate/logrotate.status /etc/logrotate.conf
# assign the exit value to the variable EXITVALUE. If the command ran succesfulyl this will be 0. 
EXITVALUE=$?
# output the variable
echo $EXITVALUE
# if the exit value does not equal 0 then print an error
if [ $EXITVALUE != 0 ]; then
    /usr/bin/logger -t logrotate "ALERT exited abnormally with [$EXITVALUE]"
fi
exit 0
```

#### Bash Script Examples

Variables: 

```bash
# assign a variabled
dt=10
# print the date
echo $dt
# print as a string using curly brackets
echo "today is the ${dt}th"
# use an $((expression)) on a variable
tomorrow=$(($dt + 1))
# store the outpout of a command
day=$(date +%d)
# or use the backticks method
month=`date +b%`
```

```bash
# show script with line details
nl {script.sh}
# give the file correct permissions
chmod +x /usr/local/bin/sys_info.sh
# debug option
bash -x /usr/local/bin/sys_info.sh
```

An example script to take a username and display the home directory:

```bash
#! /bin/sh

if [ $# -gt 1 ]; then
    echo "Error: too many arguments"
    exit 1
fi

if [ $# -eq 0 ]; then
    username=$USER
else
    username=$1
fi

userinfo=$(getent passwd $username)

if [ $? -ne 0 ]; then
    echo "Error cannot get $username"
    exit 2
fi

userhome=$(echo $userinfo | cut -f 6 -d ":")

echo "$username's home directory is $userhome"
exit 0
```

#### Command Line Arguments

| Variable  |  Represents| 
| --- |--- |
| $?   | Exit value of previous command | 
| $0   | Command/script | 
| $1 ... $9  | Arguments 1...9 | 
| ${10} and above  | Arguments >= 10 | 
| $# | Count of all arguments | 
| $* |  All arguments | 
| $$ | PID of command or script |
| shift | moves command line args one position to the left |   


#### Logical Statements 

Exit codes refer to the value returned by a program script when it finishes execution. 

```bash
# see return code for success
ls
echo $? => 1
# see return code for non-zero failure 
man
echo $?
```

#### Conditional Logic

```bash
#!/bin/bash
if [ $# -ne 2 ]
then 
    echo "Error: Invalid number of argument"
    echo "Usage: $0 source_file destination_file
exit 2
fi
echo "Script terminated"
```

We also have 

```
# if-then-else-fi
if
then 
    do
else
    do
fi

#if-then-elif-fi
if a
then
    do
    elif b then
    do
else
    do
fi
```

#### Looping Statements

* for-do-done
* while-do-done
* until-do-done