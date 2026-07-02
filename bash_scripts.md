# Bash Scripts

> Scripts built from the Bash Scripting Module - DevOps Engineering pathway

## Table of Contents

1. [Challenge 1 - Calculator](#challenge-1---calculator)
2. [Challenge 2 - File and Directory Creation](#challenge-2---file-and-directory-creation)
3. [Challenge 3 - File Checker with Permissions](#challenge-3---file-checker-with-permissions)
4. [Challenge 4 - Backup Script](#challenge-4---backup-script)

## Challenge 1 - Calculator

### Requirements
- Prompt user for two numbers
- Validate that inputs are numeric
- Handle division by zero
- Display results for addition, subtraction, multiplication, and division

### Solution

```bash
#!/bin/bash
set -u

firstnum=""
secondnum=""

take_input() {
    echo "Enter your first number:"; read firstnum
    if [[ ! $firstnum =~ ^[0-9]+$ ]]; then
        echo "Invalid input. Please provide a numeric value."
        return 1
    fi
    echo "Enter your second number:"; read secondnum
    if [[ ! $secondnum =~ ^[0-9]+$ ]]; then
        echo "Invalid input. Please provide a numeric value."
        return 1
    fi
    if [ $secondnum -eq 0 ]; then
        echo "Error: Division by zero is not allowed"
        exit 1
    fi
}

take_input

echo "Results:"
echo "$firstnum + $secondnum = $(( firstnum + secondnum ))"
echo "$firstnum - $secondnum = $(( firstnum - secondnum ))"
echo "$firstnum x $secondnum = $(( firstnum * secondnum ))"
echo "$firstnum / $secondnum = $(( firstnum / secondnum ))"
```

## Challenge 2 - File and Directory Creation

### Requirements
- Create a new directory
- Create a file inside it with content
- Display the file contents

### Solution

```bash
#!/bin/bash

mkdir bash_demo2
cd bash_demo2
echo "File contents: This file was created by a Bash script on 2024-11-29" > demo2.txt
cat ./demo2.txt
```

## Challenge 3 - File Checker with Permissions

### Requirements
- Prompt user for a filename
- Check if the file exists
- If it exists, check if it is readable, writable, and executable
- Display appropriate messages for each permission

### Solution

```bash
#!/bin/bash
set -u

filename=""

echo "Enter filename to check:"; read filename

if [ -e "$filename" ]; then
    echo "File '$filename' exists"
else
    echo "File does not exist"
    exit 1
fi

if [ -r "$filename" ]; then
    echo " ✓ File is readable"
else
    echo " ✗ File is not readable"
fi

if [ -w "$filename" ]; then
    echo " ✓ File is writeable"
else
    echo " ✗ File is not writeable"
fi

if [ -x "$filename" ]; then
    echo " ✓ File is executable"
else
    echo " ✗ File is not executable"
fi
```

## Challenge 4 - Backup Script

### Requirements
- Prompt user for a source directory
- Check if the directory exists
- Create a timestamped backup directory
- Copy all `.txt` files into the backup directory
- Display how many files were backed up

### Solution

```bash
#!/bin/bash
set -u

timestamp=$(date +%Y%m%d_%H%M%S)
backup_directory="backup_directory_$timestamp"

echo "Enter source directory:"
read directoryname

if [ ! -d ~/$directoryname ]; then
    echo "Directory does not exist. Please check the directory name and try again."
    exit 1
fi

mkdir "$backup_directory"
count=0

for file in ~/$directoryname/*.txt; do
    if [ -f "$file" ]; then
        cp "$file" "$backup_directory/"
        count=$(( count + 1 ))
    fi
done

echo "Backup directory created: $backup_directory"
echo "Backup completed! Files backed up: $count"
```

*Scripts built as part of the Bash Scripting Module - DevOps Engineering pathway*
