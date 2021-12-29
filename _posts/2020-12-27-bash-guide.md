---
layout: post
title:  "find/sed/bash-ish"
lang: en
icon: B
category: linux
tags: bash, find, sed
comments: true
---
- [Find](#find)
- [Sed](#sed)
- [Bash](#bash-ish)
    - [String operation](#string-operation)
    - [Loop](#loop)
    - [IF-ELSE](#if-else)
- [Kernal](#kernal)
    - [linux](#linux)
    - [android](#android)


---
<br/>
# Find
```
find . -type [f/d] -name prefix*suffix
```

Replace all file's extention from js to jsx if file is start with Capital letter

```
find . -type f -name "[[:upper:]]*.js" -exec sh -c 'mv "$1" "${1}x"' _ {} \;
```

---
<br/>
# Sed
```
sed -i '' 's/\(regex\)/\1, extra string/g' app/models/*.rb
```
node: `''` is needed for mac

Find all rb files and replace all `FactoryGirl` to `FactoryBot`
```
$ find ./ -type f -name *.rb -exec sed -i '' 's/FactoryGirl/FactoryBot/g'
```

---
<br/>

# Bash-ish
## String Operation

### () vs {}

(): Parentheses cause the commands to be run in a subshell.

{}: Braces cause the commands to be grouped together but not in a subshell.

### With and without $

    #!/bin/bash
    var1="A B  C   D"
    echo $var1   # A B C D
    echo "$var1" # A B  C   D

### String length

    export stringZ=abcABC123ABCabc
    echo ${#stringZ}                 # 15
    echo `expr length $stringZ`      # 15
    echo `expr "$stringZ" : '.*'`    # 15

### Substring

    export stringZ=abcABC123ABCabc
    echo ${stringZ:1}          # bcABC123ABCabc
    echo ${stringZ:7:3}        # 23A
                               # Three characters of substring.

### Substring replacement

`${parameter/pattern/string}`

- Replace first match with `string`;
- If pattern begins with '/', all matches of pattern are replaced with string
- If pattern begins with '#', it must match at the beginning
- If pattern begins with '%', it must match at the end
- If string is null, delete match(es)

      export stringZ=abcABC123ABCabc
      echo ${stringZ/abc/xyz}       # xyzABC123ABCabc
                                    # Replaces first match of 'abc' with 'xyz'.

      echo ${stringZ//abc/xyz}      # xyzABC123ABCxyz
                                    # Replaces all matches of 'abc' with # 'xyz'.

      echo ${stringZ/#abc/XYZ}      # XYZABC123ABCabc
                                    # Replaces front-end match of 'abc' with 'XYZ'.

      echo ${stringZ/%abc/XYZ}      # abcABC123ABCXYZ
                                    # Replaces back-end match of 'abc' with 'XYZ'.

### Params with default:

    # ${parameter-default}, ${parameter:-default}
    # var is not declared
    echo ${var-'1'}   # 1
    # var is declared, but null
    echo ${var:-'2'}  # 2

### arithmetic expansion

The arithmetic expansion can be performed using the double parentheses ((...)) and $((...))

    i=0
    echo "$((i + 1))" # 2


## Loop

### Loop lines over a file

    while IFS= read -r line; do
      echo "fetch daily kdata for $line"
      # extra command # python daily_kdata.py --code $line --start 1989-01-01 --end 2020-09-08
    done < filepath

### Loop file over `find`

Find all filename contains `index.less`, and rename pattern `index.less` to `style.less`

    for file in $(find src -type f -name *index.less*); do mv $file "${file%%index.less}style.less"; done


Resize all image with png to 300x300 and rename to -300.png

    for f in *; do convert "$f" -resize 300x300 "${f%%.png}-300.png";done

Loop over folder for csv files:
    i=0
    for filename in ./baostock/csv/day/*.csv
    do
      i=$((i + 1)) # number operation
      echo "dumping $filename... [$i]"
      cat $filename
    done

### Loop with different IP:

    for i in {1..8}; do curl --header "X-Forwarded-For: 1.2.3.$i" [url]; done

## IF-ELSE
Syntax
check `man test` to check all the possible test operation

    #!/bin/bash
    if [ <some test> ]
    then
      <commands>
    fi

| Operation         | Description       |
| -------------:|:-------------|
| ! EXPRESSION       | The EXPRESSION is false. |
| -n STRING          | The length of STRING is greater than zero.    |
| -z STRING          | The lengh of STRING is zero (ie it is empty).      |
| STRING1 = STRING2	 | STRING1 is equal to STRING2 |
| STRING1 != STRING2 ||
| int1 -eq int2      | INTEGER1 is numerically equal to INTEGER2 |
| int1 -gt int2      | greater than |
| int1 -lt int2      | less than |
| -d File            | FILE exists and is a directory. |
| -e File            | file exists |
| -r File            | File exists and read permission is granted |
| -s File            | FILE exists and it's size is greater than zero (ie. it is not empty). |
| -w File            | FILE exists and the write permission is granted. |
| -x FILE            | FILE exists and the execute permission is granted. |

Note:

    test 001 = 1
    echo $? # 1
    test 001 -eq 1
    echo $? # 0

    # 0       expression evaluated to true.
    # 1       expression evaluated to false or expression was missing.
    # >1      An error occurred.

# Kernal

## Linux
To find out what version of Linux (distro) you are running, try these 3 commands:

    $ cat /etc/*-release
    $ uname -a
    $ cat /proc/version

## Android
### How to check android CPU is 32 or 64

```bash
$ cat /proc/cpuinfo
```

the result will looks like this:
```
Processor : AArch64 Processor rev 4 (aarch64)
processor : 0
processor : 1
processor : 2
processor : 3
Features : fp asimd evtstrm aes pmull sha1 sha2 crc32
CPU implementer : 0x41
CPU architecture: AArch64
CPU variant : 0x0
CPU part : 0xd03
CPU revision : 4

Hardware : Amlogic
Serial : adsf
```

---
<br/>
