Stuff from LB4H that I didn't know
==================================

# Finding
`locate` looks syntactically
`whereis` only looks at bins
`which` looks for bins but only using PATH

# File Reading
`tail -n+<num>` shows tail after line number
`less` will let you search for terms with `/` like vim

# Network Management
`ifconfig <iface> <addr>` lets you define arbitrary IP addr.
You can also adjust the `netmask` and `broadcast` addrs as well.

## Spoof MAC addr
```bash
ifconfig <iface> down
ifconfig <iface> hw ether AA:BB:CC:DD:EE:FF
ifconfig <iface> up
```

# Advanced Packaging Tool (APT)
`apt-cache search <KEYWORD>` lets you search packages.
`remove` deletes the package and `purge` deletes the configs
Manage repos in /etc/apt/sources.list

# File Perms
`chown` and `chgrp` do what you think they do.

# Processes
`nice` is a utility that can manage resource alottment. Processes receive values
from -20 to +19 that determine priority. Each process has a default value of 0, \but can be changed on startup with `nice -n -+INT /path/to/bin`. For example,
`nice -n -10 /bin/slowprocess` would start a bulky process with resource 
priority.

You can set process priority with `renice` and use `renice` in `top` by pressing `r`. Need PID to `renice`.

## KILL Signals
There are different ways to kill a program, and you specify them will `kill -<INT> <PID>`.
| Name | Number | Description |
==============================
| SIGHUP | 1 | Hangup signal, stops process and restarts with same PID |
| SIGINT | 2 | Interrupt signal, a weak kill that isn't guaranteed to work |
| SIGQUIT | 3 | Core Dump, will kill and save process memory to file titled `core` in working dir. |
| SIGTERM | 15 | Terminatoin signal, default kill signal. |
| SIGKILL | 9 | Absolute kill signal. sends process resources to /dev/null. |

Use `top` or another utility to get the PID of the process you want do kill. If you can't get the PID, use `killall` and
the desired program name. 

## Scheduling Processes
`at` is a program that will let you run processes at specific times. Unsure why you would use this when cronjobs exist.

# Environment Variables
`set` works on Linux too! You will see more variables than if you are using `env`. 

If you want to make a variable change permanent just `export` it. 

`PS1` is the environment variable for ricing a terminal.
`PATH=$PATH:/new/path` adds to path. 

# Compression and Archival
`tar -cvf TarName FIletoTar File2toTar File3toTar`
`tar -tvf` will list the contents of a Tarball and `-xvf` will extract
`gzip`, `bzip2` and `compress` all archive files too.

`dd` is used to make bit-by-bit copies of storage devices, archives and even entire HDDs.
It takes an `if` in file and `of` out file parameter. You can also include block size.
This is the real keyboard cowboy utility because you can fuck your entire computer if 
you're a dipshit.

# Filesystem and Storage Management
The `/dev` directory contains the storage devices. Listing this directory will show you which devices are
attached to your computer. Each device is labeled in series, with sata drives getting the `sdX` series. 
`sda` is the first attached storage device, while `sdb` and `sdc` are the second and third respectively. 
Partitions get numbers after the letter series, so the first 3 partitions on the second storage device 
would be `sdb1 sdb2 sdb3`. 

Using `fdisk -l` or `lsblk` will show your storage devices as well as their partitions. 
`df` stands for disk free and will show the usage statistics of each drive. 
`fsck` is filesystem check and looks for problems to repair on filesystems. `-p` will autofix problems.

# Logging
Linux uses the `syslogd` daemon to log events relevant to OS operations. `/etc/rsyslog.conf` is the default
configuration location. The RULES section is where sysadmin has defined which events should be automatically
logged by the system. The log rules look like this: `facility.priority  action` where facility is the 
program, priority is the type of log (error, warning, info, etc.) and action is the write location. You can use
wildcards for the program or priority, but not for the write location. 

`logrotate` will automatically clean out old logs. It also has a .conf file in /etc

## OPSEC
While you can delete logs after a compromise, your intrusion can be extrapolated from the gaps in the
time stamps. DFIRs can recover the deleted logs as well. `shred` the logs and you will get more time, 
but DFIRs can recover data that has been subjected to `shred`. Using `shred` takes a lot of time 
because it overwrites the data with junk repeatedly just like DBAN. The default number of overwrites is 4, 
but you can increase it with `-n <INT>`, ideally to at least 10. `-f` will change the file perms to let you
overwrite if you need them to. 

NOTE: `shred` maintains the file, just makes it unreadable and (technically) unrecoverable so opening the file
shows junk data. 

If you can, just `service rsyslog stop` to prevent the generation of logs while you are in the system.

# Network OPSEC
`traceroute` lol. You need to get on someone else's wifi, log in to VPS and then VPN + TOR. 

# Wireless Fundamentals
Pringles can and wire.

`iwlist` can show you wireless networks that your host can see. Use: `iwlist <interface> scan` 
`nmcli` can list and connect to APs using `nmcli dev wifi` and `nmcli dev wifi connect <SSID> password <PASS>`

# Bluetooth
Pentesting BT devices is not something that I have ever thought about doing.
`bluez` is a debian package that helps with this. Debian defaults with `bluetoothctl` and `bluemoon` installed.
`bluez` has a couple tools: `hcitool scan` shows BT dev in range. `hcitool inq` gets MAC of devs in range.
`sdptool` is debian default bt scanner that needs the MAC to run inquire. 

RESEARCH `l2ping` vs `ping` differences. 

# Kernel Management
If `uname` is not installed you can read `/proc/version` 
`sysctl` lets you tune the kernel. Changes only save if you alter `/etc/sysctl.conf`
Kernel Modules are listed with `lsmod` and details are discovered with `modinfo <MODNAME>`
`modprobe -a` and `-r` add and remove kernel modules. `dmesg | grep <MOD>` confirms module install.

# Startup Jobs
After the kernel inits and env is set up, `rc` scripts are run. Look into these to stop apache and other shit from clogging startup.
