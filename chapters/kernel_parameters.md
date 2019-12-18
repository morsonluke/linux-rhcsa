#  Tuning Kernel Parameters, Reporting System Usage, and Logging Remotely

Run-time parameters control the kernel behavior while the system is operational. 

```
# see active runtime parameters
sysctl -a
# see system detault settings
cat /usr/lib/sysctl.d/00-system.conf
# see value of parameter
sysctl sunrpc.tcp_fin_timeout || cat /proc/sys/sunrpc/tcp_fin_timeout
# change the value 
sysctl -w sunrpc.tcp_fin_timeout=18 || echo 18 > /proc/sys/sunrpc/tcp_fin_timeout
# change value to 16 persistently append {parameter}={value} 
vi /etc/sysctl.conf
# load the value
sysctl -p
```

Boot-time paramaters affect the boot behaviour of the kernel. They are supplied to the Kernel via the GRUB 2 interface.

```
# view boot string and command-line options
cat /proc/cmdline
```

#### System Usage Reports

```
yum -y install systat
yum -y install dstat
# see results
dstat -cdmn
```



