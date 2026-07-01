# Bash Scripting Module Notes

> Study notes

## Table of Contents

1. [Running Scripts](#1-running-scripts)
2. [Comments](#2-comments)
3. [Variables](#3-variables)
4. [Parameters](#4-parameters)
5. [Arithmetic](#5-arithmetic)
6. [If Statements and Conditionals](#6-if-statements-and-conditionals)
7. [Loops](#7-loops)
8. [Functions and Input](#8-functions-and-input)
9. [Error Handling and Exit Codes](#9-error-handling-and-exit-codes)
10. [Environment Variables and PATH](#10-environment-variables-and-path)
11. [Working With Files](#11-working-with-files)

## 1. Running Scripts

There are three ways to run a script. You only need `sh` or `bash`(or whatever shell it is) if there is no shebang line at the top of the script. Adding a script to `$PATH` means you can run it from anywhere without specifying the full path.

```bash
sh example.sh       # run with sh
bash example.sh     # run with bash
./example.sh        # run directly (requires shebang + execute permission)
```

The shebang line tells the system which interpreter to use:

```bash
#!/bin/bash
```

To run a script from anywhere:

```bash
chmod +x my_script.sh
sudo mv my_script.sh /usr/local/bin/
```

## 2. Comments

Anything after `#` on a line is ignored by bash. Comments are used to explain what your code does so anyone reading it understands without having to reverse-engineer the logic.

```bash
# This is a single line comment

: '
This is a
multi-line comment
'
```

## 3. Variables

Variables store values you want to reuse. They make scripts easier to read and mean you only have to change a value in one place instead of everywhere it appears.

```bash
example=10
example2="hello"
echo "Number: $example and Word: $example2"
# prints: Number: 10 and Word: hello
```

Rules:
- No spaces around `=`
- Use `$variable_name` to reference a variable
- Use lowercase with underscores: `my_variable`
- Always quote variables when using them: `"$variable"`

## 4. Parameters

Parameters are values passed into a script when it is called. They make scripts flexible and reusable so the same script can handle different inputs without being rewritten.

```bash
#!/bin/bash

echo "Script name: $0"
echo "First arg:   $1"
echo "Second arg:  $2"
echo "Arg count:   $#"
echo "All args:    $@"
```

Run with:
```bash
./example.sh apple banana
```

| Variable | Meaning |
|---|---|
| `$0` | Script name |
| `$1`, `$2`... | Arguments |
| `$#` | Total number of arguments |
| `$@` | All arguments as separate words |
| `$*` | All arguments as one string |
| `$?` | Exit code of last command |

## 5. Arithmetic

Use `$(( ))` for arithmetic. Shell arithmetic is integer only so there are no decimals. Use `bc` if you need decimal calculations.

```bash
echo $(( 2 + 3 ))    # 5
echo $(( 10 - 3 ))   # 7
echo $(( 4 * 3 ))    # 12
echo $(( 10 / 2 ))   # 5
echo $(( 10 % 3 ))   # 1 (remainder)
echo $(( 2 ** 8 ))   # 256 (power)
```

Storing results:
```bash
total=$(( price * quantity + tax ))
```

Increment shorthands:
```bash
(( count++ ))     # add 1
(( count-- ))     # subtract 1
(( count += 5 ))  # add 5
(( count -= 5 ))  # subtract 5
(( count *= 2 ))  # multiply by 2
(( count /= 2 ))  # divide by 2
```

Using with parameters:
```bash
#!/bin/bash
bill="$1"
people="$2"
each=$(( bill / people ))

echo "Total bill: $bill"
echo "People:     $people"
echo "Per person: $each"
```

## 6. If Statements and Conditionals

Conditionals let your script make decisions. Use them to check if a file exists before reading it, validate input before using it, or check a condition before running a block of code.

Basic syntax:
```bash
if [ condition ]; then
    # code block
fi
```

Comparison operators:

| Operator | Meaning |
|---|---|
| `-eq` | equals |
| `-ne` | not equal to |
| `-lt` | less than |
| `-gt` | greater than |
| `-le` | less than or equal to |
| `-ge` | greater than or equal to |
| `==` | string equals |
| `!=` | string not equal |
| `&&` | and |
| `\|\|` | or |

Else:
```bash
if [ $age -gt 18 ]; then
    echo "You are an adult"
else
    echo "You are not an adult"
fi
```

Elif:
```bash
if [ $age -gt 18 ]; then
    echo "You are an adult"
elif [ $age -eq 18 ]; then
    echo "You just turned 18!"
else
    echo "You are not an adult"
fi
```

Nested if - a check within a check. The inner if only runs if the outer if was true first:
```bash
if [ $age -gt 18 ]; then
    echo "You are an adult"
    if [ $age -gt 65 ]; then
        echo "You are also a senior"
    else
        echo "You are not yet a senior"
    fi
else
    echo "You are not an adult"
fi
```

## 7. Loops

Loops repeat a block of code. Use while for conditions and counters. Use for for lists and arrays.

While loop - runs as long as the condition is true. Every while loop needs a start value, a condition, and an increment otherwise it runs forever:
```bash
count=1

while [ $count -le 5 ]; do
    echo "Count: $count"
    (( count++ ))
done
```

While true - runs forever until break stops it:
```bash
count=1

while true; do
    echo "Count: $count"
    (( count++ ))
    if [ $count -eq 4 ]; then
        break
    fi
done
```

For loop - array:
```bash
fruits=("apple" "banana" "cherry")

for fruit in "${fruits[@]}"; do
    echo "Fruit: $fruit"
done
```

For loop - number range using brace expansion:
```bash
for number in {1..5}; do
    echo "Number: $number"
done
```

C-style for loop - useful when you need more control over the counter:
```bash
for (( i=1; i<=5; i++ )); do
    echo "$i"
done
```

Break and continue:
```bash
# continue - skip current iteration, keep looping
for (( i=1; i<=5; i++ )); do
    if [ $i -eq 3 ]; then
        continue
    fi
    echo "Number: $i"
done

# break - stop the loop entirely
while true; do
    (( count++ ))
    if [ $count -eq 4 ]; then
        break
    fi
done
```

## 8. Functions and Input

Functions let you write reusable blocks of code. They keep scripts modular and clean so you can call the same logic multiple times without repeating it.

Syntax:
```bash
function_name() {
    # code block
}
```

Rules:
- Define the function before calling it
- Call it by typing its name with no brackets
- Keep names lowercase with underscores
- Never use hyphens in function names

With parameters:
```bash
greet() {
    echo "Hello $1!"
}

greet "Alice"
greet "Bob"
```

Local variables - keeps variables scoped inside the function so they don't overwrite global variables with the same name:
```bash
name="Alice"

greet() {
    local name="Bob"
    echo "Hello $name"  # prints Bob
}

greet
echo $name    # prints Alice - unchanged
```

User input:
```bash
read -p "What is your name? " name
echo "Hello $name!"
```

## 9. Error Handling and Exit Codes

Exit codes tell you if a command succeeded or failed. Every command returns one when it finishes. Good error handling stops your script the moment something goes wrong instead of letting it continue with bad data.

| Code | Meaning |
|---|---|
| `0` | Success |
| `1` | General error |
| `2` | Misuse of command |
| `127` | Command not found |

`$?` holds the exit code of the last command run. Store it immediately before anything else runs and changes it:
```bash
validate_age "$user_age"
exit_code=$?

if (( exit_code != 0 )); then
    echo "Input validation failed."
fi
```

Using exit to stop a script early:
```bash
if [ $num2 -eq 0 ]; then
    echo "Error: Division by zero is not allowed"
    exit 1
fi
```

Set commands - place at the top of scripts after the shebang to make them stricter:

| Command | Behaviour |
|---|---|
| `set -e` | Exit immediately if any command fails |
| `set -u` | Exit if an undefined variable is used |
| `set -x` | Print each command before running it |
| `set -o pipefail` | Fail if any command in a pipe fails |
| `set -euxo pipefail` | All of the above combined |

```bash
#!/bin/bash
set -euxo pipefail
```

## 10. Environment Variables and PATH

Environment variables store system-wide values. They are used to pass configuration into scripts without hardcoding values. PATH changes made in the terminal are temporary and lost when the session ends so they need to be added to your config file to make them permanent.

Making PATH changes permanent:
```bash
echo 'export PATH=$PATH:~/my_scripts' >> ~/.bashrc
source ~/.bashrc
```

Use single quotes so `$PATH` is written literally into the file and expands correctly at runtime.

Common environment variables:

```bash
$HOME        # home directory
$USER        # current logged in user
$SHELL       # current shell
$HOSTNAME    # machine name
$PWD         # current working directory
$PATH        # directories searched for commands
$?           # exit code of last command
$$           # process ID of current shell
$EDITOR      # default text editor
$LANG        # system language
$TZ          # timezone
```

View variables:
```bash
echo $HOME      # view a specific variable
printenv        # view all environment variables
```

## 11. Working With Files

Reading and writing files is a core part of scripting. Reading line by line is the safest method as it preserves spaces and special characters. Checksums verify that a file has not been changed or corrupted.

Reading an entire file:
```bash
cat file.txt
```

Reading line by line - the safest method. `IFS=` preserves spaces, `-r` treats backslashes as literal characters:
```bash
read_file() {
    local file_path="$1"

    while IFS= read -r line; do
        echo "$line"
    done < "$file_path"
}

read_file "./log.txt"
```

Writing to a file:
```bash
write_to_file() {
    local file_path="$1"
    local data="$2"

    echo "$data" >> "$file_path"
}

write_to_file "log.txt" "New entry"
```

| Operator | Behaviour |
|---|---|
| `>` | Overwrites file with new content |
| `>>` | Appends new content to end of file |

File checksums - a unique fingerprint of a file. If the file changes even slightly the checksum changes. Used to verify files have not been tampered with or corrupted during transfer:

```bash
md5sum file.txt       # MD5 checksum
sha256sum file.txt    # SHA256 checksum (more secure)
```

Compare checksums:
```bash
compare_checksums() {
    local checksum1="$1"
    local checksum2="$2"

    if [[ "$checksum1" == "$checksum2" ]]; then
        echo "Checksums match. File is intact."
    else
        echo "Checksums do not match. File may be corrupted."
    fi
}
```

*Notes compiled during Bash Scripting Module - DevOps Engineering pathway*
