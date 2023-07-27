---
description: Jesus Christ it's Jason Bourne Again Shell
---

# 🧑🦯 Bash

## General Syntax

```
--- Code if elif and then --- 
echo "Working" 
if [ $# -eq 0 ] 
then  
      echo "Please Enter The Code To Decode" 
exit 1 
elif [ $# = 1 ] 
then  
      code=$1 
echo $code 
fi

--- for Loop in Bash --- 
for i in {1..5} 
do 
   echo "Welcome $i times" 
done 
 
--- for inloop --- 
for ip in "10.10.10.170 10.10.10.174 10.10.10.175" 
do 
    ping -c 1 $ip 
done 
 
--- for loop even numbers --- 
for i in {0..10..2} 
do 
  echo "Welcome $i times" 
done  

---While Loop --- 
stat=1 
while [ $stat -eq 1 ] 
do 

--- updating variables --- 
code=$1 
code=$(echo $code | base64) 

--- Variable length --- 
htb="HackTheBox" 
echo ${#htb} 

--- Arguments --- 
./script.sh ARG1 ARG2 ARG3 ARG4
      ^$0    ^$1  ^$2  ^$3  ^$4
      
--- Internal Field Separators ---
$# Holds number of args passed to script
$@ Retrieves the list of command line arguments
$$ The PID of the currently executing process
$? Exit Status of the script. 0 good, 1 bad

--- Arrays in Bash ---
arr=(1 2 3 4)
for (( i=0,i<3,i++ ))
do 
echo ${arr[$i]}
done

--- Functions ---
$!/bin/bash
foo(){
    echo "Hello World"
}
foo
```

## Operations

| Operation                     | String | Integer |
| ----------------------------- | ------ | ------- |
| `is equal to`                 | `==`   | `-eq`   |
| `is not equal to`             | `!=`   | `-ne`   |
| `is less than`                | `<`    | `-lt`   |
| `is greater than`             | `>`    | `-gt`   |
| `is null`                     | `-z`   |         |
| `is not null`                 | `-n`   |         |
| `is greater than or equal to` |        | `-ge`   |

Greater than and Less than operations on strings simply order by ASCII alphabetical order.

### File Operators

| Operator | Description                                    |
| -------- | ---------------------------------------------- |
| `-e`     | `check file existence`                         |
| `-f`     | `check if object is file`                      |
| `-d`     | `check if object is directory`                 |
| `-L`     | `check if file is symlink`                     |
| `-N`     | `check if file was modified after last read`   |
| `-O`     | `check if current user owns file`              |
| `-G`     | `check if file's groupid matches current user` |
| `-s`     | `check if filesize > 0`                        |
| `-r`     | `check if file has read permission`            |
| `-w`     | `check if file has write permission`           |
| `-x`     | `check if file has execute permission`         |

