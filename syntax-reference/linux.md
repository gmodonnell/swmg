---
description: The idea is to check this page less and less
---

# 🐧 Linux

<details>

<summary>grep</summary>

```
cat /etc/passwd | grep "/bin/bash" --include 
cat /etc/passwd | grep -v "false\|nologin" --exclude 
| grep -c 'string' (only count lines with string)
```

</details>

<details>

<summary>wc</summary>

```
wc:
-l lines
-m characters
-w words
```

</details>

<details>
<summary>cURL</summary>

```
-O download webpage
-s suppress output
-k skip cert check (to navigate around invalid SSL)
-v(vv) Verbosity, 3 Levels
-I HEAD request, only headers
-i Headers and Response
-H custom header
-A custom user-agent
-u credentials in user:pass format
-X Specify Method
-d POST data
-b provide cookie
```

</details>

| Code | Permissions     |
| ---- | --------------- |
| 0    | No Permission   |
| 1    | Execute         |
| 2    | Write           |
| 3    | Execute & Write |
| 4    | Read            |
| 5    | Read & Execute  |
| 6    | Read & Write    |
| 7    | rwx             |

```
who - logged in users
apropos <search term> - bins and their uses
"cd -" takes you to your previous directory
```

## find
```
-type f (only files)
-(i)name *.tar (by name)
-user root (by owner)
-size +20k (over/under certain size)
-newermt 2023-12-31 (newer than date)
-exec ls -al () \; (executes a command)
2>/dev/null (error management)
```
If you include multiple of the same parameter you have to define it twice, so finding files that are bigger than 20k but smaller than 30k requires you define `-size` twice in the command. 

## Stream Direction
0,1 and 2 are used to describe STDIN, OUT and ERR. You can direct these with <> symbols, which is what the `2>/dev/null` does whenever you use find. You can direct streams to different files in one command like this: `find /etc/ -name shadow 2> stderr.txt 1> stdout.txt`

This is also how you feed STDIN to a command, like `cat < stdout.txt`. 

## Mount Fileshare
NFS shares set up on remote address are mounted to local filesystem like this: `mount <IP>:/file/to/share ~/localpath`
Then you can access it like a local directory.

### RSync Backup
`rsync -avz -e ssh /path/to/mydirectory user@backup_server:/path/to/backup/directory`

## Linux Security
Learn SELinux, AppArmor, and TCP Wrappers :)

SELinux labels everything and then applies policy to it. TCP Wrappers allow sysadmins to determine whihc services are allowed access to each system. You can find these settings in `/etc/hosts.allow` and `/etc/hosts.deny`