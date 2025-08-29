---
layout: post
title:  "find/sed/bash/csv"
lang: en
icon: B
category: linux
tags: bash, find, sed
comments: true
---

# Linux/Bash Command Reference Guide

A comprehensive reference for common Linux and Bash commands, utilities, and system information.

## Table of Contents

- [Find](#find)
- [Sed](#sed)
- [Bash Scripting](#bash-scripting)
  - [String Operations](#string-operations)
  - [Loops](#loops)
  - [Conditionals (IF-ELSE)](#conditionals-if-else)
- [Text Processing](#text-processing)
  - [Remove ^M Characters in Vim](#remove-m-characters-in-vim)
  - [Convert CSV to JSON](#convert-csv-to-json)
- [System Information](#system-information)
  - [Linux](#linux)
  - [Android](#android)

---

## Find

### Basic Syntax
```bash
find . -type [f/d] -name "pattern"
```

### Common Examples

**Find files and directories by name:**
```bash
# Find files starting with "test"
find . -type f -name "test*"

# Find directories containing "backup"
find . -type d -name "*backup*"
```

**Replace file extensions for files starting with capital letters:**
```bash
# Change .js to .jsx for files starting with capital letters
find . -type f -name "[[:upper:]]*.js" -exec sh -c 'mv "$1" "${1}x"' _ {} \;
```

**Find and process multiple files:**
```bash
# Find all Python files modified in last 7 days
find . -type f -name "*.py" -mtime -7

# Find and delete empty directories
find . -type d -empty -delete
```

---

## Sed

### Basic Syntax
```bash
sed -i 's/pattern/replacement/flags' file
```

**Note:** On macOS, use `sed -i ''` (with empty quotes)

### Common Examples

**Basic find and replace:**
```bash
# Replace text in Ruby files (with capture group)
sed -i '' 's/\(regex\)/\1, extra string/g' app/models/*.rb
```

**Mass find and replace across multiple files:**
```bash
# Replace FactoryGirl with FactoryBot in all Ruby files
find . -type f -name "*.rb" -exec sed -i '' 's/FactoryGirl/FactoryBot/g' {} \;
```

**Advanced sed operations:**
```bash
# Delete lines containing pattern
sed -i '/pattern/d' file.txt

# Insert text at specific line number
sed -i '3i\New line text' file.txt

# Replace only first occurrence per line
sed 's/old/new/' file.txt
```

---

## Bash Scripting

### String Operations

#### Parentheses vs Braces
- `()`: Commands run in a subshell (isolated environment)
- `{}`: Commands grouped together in current shell

#### Variable Quoting
```bash
#!/bin/bash
var1="A B  C   D"
echo $var1   # Output: A B C D (word splitting occurs)
echo "$var1" # Output: A B  C   D (preserves spacing)
```

#### String Length
```bash
export stringZ="abcABC123ABCabc"
echo ${#stringZ}                 # 15
echo $(expr length $stringZ)     # 15
echo $(expr "$stringZ" : '.*')   # 15
```

#### Substrings
```bash
export stringZ="abcABC123ABCabc"
echo ${stringZ:1}          # bcABC123ABCabc (from position 1)
echo ${stringZ:7:3}        # 23A (3 characters starting at position 7)
```

#### String Replacement
**Syntax:** `${parameter/pattern/string}`

- Single `/`: Replace first match
- Double `//`: Replace all matches
- `#` prefix: Match at beginning
- `%` prefix: Match at end
- Empty replacement: Delete matches

```bash
export stringZ="abcABC123ABCabc"
echo ${stringZ/abc/xyz}       # xyzABC123ABCabc (first match)
echo ${stringZ//abc/xyz}      # xyzABC123ABCxyz (all matches)
echo ${stringZ/#abc/XYZ}      # XYZABC123ABCabc (beginning)
echo ${stringZ/%abc/XYZ}      # abcABC123ABCXYZ (end)
```

#### Default Parameters
```bash
# ${parameter-default}: Use default if unset
# ${parameter:-default}: Use default if unset or empty

# var is unset
echo ${var-'default'}   # default

# var is set but empty
var=""
echo ${var:-'default'}  # default
```

#### Arithmetic Expansion
```bash
i=5
echo $((i + 1))    # 6
echo $((i * 2))    # 10
```

### Loops

#### Reading Files Line by Line
```bash
# Process each line of a file
while IFS= read -r line; do
    echo "Processing: $line"
    # Add your commands here
done < "filename.txt"
```

#### Looping with Find Results
```bash
# Rename files found by find
for file in $(find src -type f -name "*index.less*"); do
    mv "$file" "${file%%index.less}style.less"
done

# Process images
for f in *.png; do
    convert "$f" -resize 300x300 "${f%%.png}-300.png"
done
```

#### Processing CSV Files
```bash
i=0
for filename in ./data/*.csv; do
    i=$((i + 1))
    echo "Processing $filename... [$i]"
    # Process file here
done
```

#### Network Operations
```bash
# Test with different IP addresses
for i in {1..8}; do
    curl --header "X-Forwarded-For: 1.2.3.$i" "http://example.com"
done
```

### Conditionals (IF-ELSE)

#### Basic Syntax
```bash
#!/bin/bash
if [ condition ]; then
    # commands
elif [ another_condition ]; then
    # commands
else
    # commands
fi
```

#### Test Operations

| Operation | Description |
|-----------|-------------|
| `! EXPRESSION` | Expression is false |
| `-n STRING` | String length > 0 |
| `-z STRING` | String length = 0 |
| `STRING1 = STRING2` | Strings are equal |
| `STRING1 != STRING2` | Strings are not equal |
| `int1 -eq int2` | Integers are equal |
| `int1 -gt int2` | Greater than |
| `int1 -lt int2` | Less than |
| `-d FILE` | File is directory |
| `-e FILE` | File exists |
| `-f FILE` | File exists and is regular file |
| `-r FILE` | File is readable |
| `-w FILE` | File is writable |
| `-x FILE` | File is executable |
| `-s FILE` | File exists and is not empty |

#### Examples
```bash
#!/bin/bash
if [ -f "config.txt" ]; then
    echo "Config file exists"
fi

if [ $# -eq 0 ]; then
    echo "No arguments provided"
    exit 1
fi

# Numeric comparison (note: string vs numeric)
test "001" = "1"    # false (string comparison)
test "001" -eq 1    # true (numeric comparison)
```

---

## Text Processing

### Remove ^M Characters in Vim

When working with files that have Windows line endings (CRLF), you may see `^M` characters in Vim.

**Solution:**
1. **Force DOS format reading:**
   ```vim
   :e ++ff=dos
   ```
   This tells Vim to read the file again with DOS format, removing CRLF endings.

2. **Set Unix format:**
   ```vim
   :set ff=unix
   ```

3. **Save and quit:**
   ```vim
   :wq
   ```

**Alternative methods:**
```bash
# Using dos2unix command
dos2unix filename.txt

# Using sed
sed -i 's/\r$//' filename.txt

# Using tr
tr -d '\r' < input.txt > output.txt
```

### Convert CSV to JSON

#### Basic CSV to JSON Array
```bash
# Convert CSV to JSON array of objects
cat data.csv | python -c "
import csv, json, sys
print(json.dumps([dict(r) for r in csv.DictReader(sys.stdin)])
" | jq
```

#### Create Key-Value Object from CSV
```bash
# Convert CSV to single object using one column as keys
cat nace-es-dedup.csv | python -c "
import csv, json, sys
print(json.dumps([dict(r) for r in csv.DictReader(sys.stdin)]))
" | jq 'map({(.Code): .ES}) | add'
```

#### Advanced Processing Examples
```bash
# Filter and transform while converting
cat data.csv | python -c "
import csv, json, sys
data = [dict(r) for r in csv.DictReader(sys.stdin)]
filtered = [item for item in data if item.get('status') == 'active']
print(json.dumps(filtered))
" | jq '.[] | select(.price > 100)'

# Convert with custom field mapping
cat inventory.csv | python -c "
import csv, json, sys
reader = csv.DictReader(sys.stdin)
mapped = []
for row in reader:
    mapped.append({
        'id': row['item_id'],
        'name': row['item_name'],
        'cost': float(row['price'])
    })
print(json.dumps(mapped))
" | jq
```

---

## System Information

### Linux

**Check Linux distribution and version:**
```bash
# Method 1: Release information
cat /etc/*-release

# Method 2: System information
uname -a

# Method 3: Kernel version
cat /proc/version

# Method 4: OS info (newer systems)
hostnamectl

# Method 5: LSB information
lsb_release -a
```

**Additional system info commands:**
```bash
# CPU information
cat /proc/cpuinfo

# Memory information
cat /proc/meminfo
free -h

# Disk usage
df -h
lsblk

# System uptime
uptime

# Kernel modules
lsmod
```

### Android

#### Check CPU Architecture (32-bit vs 64-bit)
```bash
# Primary method
cat /proc/cpuinfo

# Alternative methods
uname -m
getprop ro.product.cpu.abi
```

**Sample output for 64-bit ARM:**
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
Serial : [redacted]
```

#### Android-specific Commands
```bash
# Android version
getprop ro.build.version.release

# Device model
getprop ro.product.model

# Build information
getprop ro.build.fingerprint

# ABI information
getprop ro.product.cpu.abilist

# Check if device is rooted
su -c "echo 'Device is rooted'"
```

---

## Additional Tips

### Performance and Efficiency
- Use `find` with `-exec` sparingly on large directories
- Consider `xargs` for better performance with multiple files
- Use `parallel` for CPU-intensive operations across multiple files
- Quote variables to prevent word splitting: `"$variable"`

### Safety Practices
- Always test commands on sample data first
- Use `--dry-run` options when available
- Make backups before bulk operations
- Use version control for important changes

### Debugging
- Add `set -x` to bash scripts for debugging
- Use `echo` statements to verify variable values
- Check exit codes with `$?`
- Use `shellcheck` to validate bash scripts