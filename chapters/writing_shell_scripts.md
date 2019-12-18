# Writing Shell Scripts

Shell scripts are text files that contain Linux commands and control structures for the automation of tasks.

#### Bash Script 1

```
#!/bin/bash
echo "Show Info"
echo "======================="
echo 
echo "System info:"
/usr/bin/hostnamectl
echo
echo "Users logged into the system:"
/usr/bin/who
```

```
# show script with line details
nl {script.sh}
# give the file correct permissions
chmod +x /usr/local/bin/sys_info.sh
# debug option
bash -x /usr/local/bin/sys_info.sh
```

#### Command Line Arguments

| Variable  |  Represents| 
| --- |--- |
| $0   | Command/script | 
| $1 ... $9  | Arguments 1...9 | 
| ${10} and above  | Arguments >= 10 | 
| $# | Count of all arguments | 
| $* |  All arguments | 
| $$ | PID of command or script |
| shift | moves command line args one position to the left |   


#### Logical Statements 

Exit codes refer to the value returned by a program script when it finishes execution. 

```
# see return code for success
ls
echo $? => 1
# see return code for non-zero failure 
man
echo $?
```

#### Conditional Logic

```
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