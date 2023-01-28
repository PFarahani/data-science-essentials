<img src="assets/linux-logo.svg" width="250" height="250">


# Hands-on Introduction to Linux Commands and Shell Scripting  <!-- omit from toc -->

## Table of contents <!-- omit from toc -->

- [1. Introduction to Linux](#1-introduction-to-linux)
  - [1.1. Overview of Linux Architecture](#11-overview-of-linux-architecture)
  - [1.2. Creating and Editing Texts](#12-creating-and-editing-texts)
- [2. Linux Commands](#2-linux-commands)
  - [2.1. Information Commands](#21-information-commands)
  - [2.2. File and Directory Navigation Commands](#22-file-and-directory-navigation-commands)
  - [2.3. File and Directory Management Commands](#23-file-and-directory-management-commands)
  - [2.4. Viewing File Content](#24-viewing-file-content)
  - [2.5. Customizing View of File Contents](#25-customizing-view-of-file-contents)
  - [2.6. File Archiving and Compression Commands](#26-file-archiving-and-compression-commands)
  - [2.7. Networking Commands](#27-networking-commands)
- [3. Introduction to Shell Scripting](#3-introduction-to-shell-scripting)
  - [3.1. Filters, Pipes, and Variables](#31-filters-pipes-and-variables)
  - [3.2. Useful Features of the Bash Shell](#32-useful-features-of-the-bash-shell)
  - [3.3. Scheduling Jobs Using Cron](#33-scheduling-jobs-using-cron)
- [4. Examples](#4-examples)
  - [4.1. Example 1](#41-example-1)
  - [4.2. Example 2](#42-example-2)
  - [4.3. Example 3](#43-example-3)


<br>
<br>

****************
## 1. Introduction to Linux

- Unix is a family of operating systems dating from the 1960s
- Linux was originally developed in the 1980s as a free, open-source alternative to Unix
- Linux is multi-user, portable, and supports multitasking
- Linux is widely used today in mobile devices, supercomputers, data centers, and cloud servers

### 1.1. Overview of Linux Architecture

The Linux system comprises five distinct layers:
1. **User:** The user is the person using the Linux system
2. **Application:** Users interact with the system via the application layer that includes system daemons, shells, user apps, and tools 
3. **Operating System:** The applications communicate with the operating system to perform tasks. The OS is responsible for jobs that are vital for system stability such as job scheduling and keeping track of time
4. **Kernel:** All Linux operating systems are built on top of the Linux kernel, which performs the most vital lower-level jobs. The kernel is the core component of the operating system and is responsible for managing memory, processing, security, and so on
5. **Hardware:** The kernel interacts with the hardware layer, which includes all the physical electronic devices in the computer such as processors, memory modules, input devices, and storage

The top level of the filesystem is the `root` directory, symbolized by a forward slash (/). Below this, is a tree-like structure of the directories and files in the system. And the filesystem assigns appropriate access rights to the directories and files.

The very top of the Linux filesystem is the `root` directory, which contains many other directories and files. One of the key directories is `/bin`, which contains user binary files. Binary files contain the code your machine reads to run programs and execute commands. Other key directories include `/usr`, which contains user programs, `/home`, which is your personal working directory where you should store all your personal files, `/boot`, which contains your system boot files, the instructions vital for system startup, and `/media`, which contains files related to temporary media such as CD or USB drives that are connected to the system.

### 1.2. Creating and Editing Texts

There are many editors to choose from and they can be grouped into two main categories: Command-line text editors and GUI (Graphical User Interface) text editors

1. Command-line text editors
   - GNU nano 
   - vi
   - vim
2. GUI-based editors
   - gedit
   - emacs (can be used in both GUI and command-line mode)


<br>
<br>

****************
## 2. Linux Commands

### 2.1. Information Commands

Some common shell commands for getting information include:

- `whoami` → returns the user's username
- `id` → returns the current user and group IDs
  ```sh
  # User's numerical ID
  id -u
  # User's name
  id -u -n
  ```
- `uname` → returns the operating system information,
  ```sh
  # OS name and its version
  uname -s -r 
  # All information
  uname -v
  ```
- `df` → shows information about mounted file systems
  ```sh
  # Disk usage in human readable format
  df -h
  # Disk usage for a specific directory (~ refers to /home) 
  df -h ~
  ```
- `ps` → displays running processes and their IDs
  ```sh
  # Show processes only with root privileges
  ps -u root 
  ```
- `top` → displays running processes and resource usage including memory, CPU, and IO
  ```sh
  # Top 3 running tasks
  top -n 3
  ```
- `echo` → prints string or variable values
  ```sh
  # Print string
  echo "hello world!"
  # Print path of a variable
  echo $PATH variable_name
  ```
- `date` → displays system date and time
  ```sh
  date
  # Fri 27 Jan 2023 17:52:49 EDT
  date "+%j day of %Y"
  # 027 day of 2023
  date "+It's %A, the %j day of %Y!"
  # It's Friday, the 027 day of 2023!
  ```
- `man` → fetches the reference manual for any shell command



### 2.2. File and Directory Navigation Commands

Very common shell commands for navigating and working with directories include:

- `ls` → list files and directories
  ```sh
  # List the content of a specific directory
  ls Downloads
  # Longer details
  ls -l
  # List recursively
  ls -R
  ```
- `pwd` → prints the current, or "present working directory"
- `cd` → changes the current directory to another directory
- `find` → used to find files matching a pattern in the current directory tree
  ```sh
  # Find within working directory
  find . -name "a.txt"
  # Case-insensitive
  find . -iname "a.txt"
  ```

### 2.3. File and Directory Management Commands

Some common shell commands for working with files include:

- `mkdir` → makes a new directory
- `rm` → remove file
  ```sh
  # Remove a directory along with its child objects
  rm -r folder1
  ```
- `rmdir` → removes an entire directory. It's a safer option than `rm -rf`
- `touch` → create empty file, update file timestamp
- `cp` → copy file
  ```sh
  # Copy files
  cp /source/file /dest/filename
  cp /source/file /dest/
  # Copy directories
  cp -r /source/dir /dest/dir/
  ```
- `mv` → change file name or path
  ```sh
  mv /source/file /dest/filename
  ```
- `chmod` → change/modify file permissions
  ```sh
  # Make a file executable
  chmod +x my_script.sh
  ```

### 2.4. Viewing File Content

- `cat` → prints the entire contents of a file
- `more` → used to print file contents one page at a time
- `head` → for printing just the first "N" lines of a file
  ```sh
  # First 3 lines
  head -n 3
  ```
- `tail` → for printing the last "N" lines of a file
- `wc` → get count of lines, words, characters in file
  ```sh
  # Number_of_lines Number_of_words Number_of_characters(including new lines)
  wc file.txt
  # Line count
  wc -l file.txt
  # Word count
  wc -w file.txt
  # Charcter count
  wc -c file.txt
  ```

### 2.5. Customizing View of File Contents 

- `sort` → sorts lines in a file
  ```sh
  # Sort in reverse order
  sort -r file.txt
  ```
- `uniq` → filter out repeated lines only if they are consecutive
- `grep` → return lines in file matching pattern using regular expression
  ```sh
  # Case insensitive search
  grep -i
  ```
- `cut` → extracts a section from each line
  ```sh
  # Extract charcters 2 to 9
  cut -c 2-9 file.txt 
  # Extract 2nd field after splitting text using a delimiter
  cut -d ';' -f2 file.txt 
  ```
- `paste` → merge lines from different files
  ```sh
  paste first.txt last.txt yob.txt
  # Specify a custom delimiter
  paste -d ',' first.txt last.txt yob.txt
  ```

### 2.6. File Archiving and Compression Commands

**Archives:**
- Store rarely used information and preserve it
- Are a collection of data files and directories stored as a single file
- Make the collection more portable and serve as a backup in case of loss or corruption
**File compression:**
- Reduces file size by reducing information redundancy
- Preserves storage space, speeds up data transfer, and reduces bandwidth load

Shell commands related to file compression and archiving applications include:

- `tar` → archive a set of files
  ```sh
  # Create a new archive and interpret its input from the file rather than the default
  tar -cf notes.tar notes
  # Compress it
  tar -czf notes.tar.gz notes
  # Check the contents of tar file
  tar -tf notes.tar
  # Extract files/folders
  tar -xf notes.tar notes
  # Unpack and decompress
  tar -xzf notes.tar.gz notes
  ```
- `zip` → compresses a set of files
  ```sh
  zip notes.zip notes
  ```
- `unzip` → extracts files from a compressed or zipped archive
  ```sh
  unzip notes.zip
  ```
**Note:** `tar` first bundles files/folders and then using `-czf` option, compresses them, while `zip` compresses the files and then bundles them.

### 2.7. Networking Commands

Networking applications include the following:
- `hostname` → prints the host name
  ```sh
  # host's ip address
  hostname -i
  ```
- `ifconfig` → displays or configures network interfaces on the system
  ```sh
  # Information about ethernet adabtor
  ifconfig eth0
  ```
- `ping` → sends packets to a URL and prints the response
  ```sh
  ping www.google.com
  # Return a certain number of ping results
  ping -c 5 www.google.com
  ```
- `curl` → displays the contents of a file located at a URL
  ```sh
  # Return HTML content of the landing page
  curl www.google.com
  # Write the content to a file
  curl www.google.com -o google.txt
  ```
- `wget` → command can be used to download a file from a URL


<br>
<br>

****************
## 3. Introduction to Shell Scripting

A shell script is an executable text file with an interpreter directive aka "shebang" directive which has the following form:

```sh
#!/path/to/executable/program [optional-arg]
```

For example:

Shell script directives:

```sh
#!/bin/sh
#!/bin/bash
```

Python script directive:

```sh
#!/usr/bin/env python3
```

### 3.1. Filters, Pipes, and Variables

Filters are shell commands, which transform input data into output data. Examples are `wc`, `cat`, `more`, `head`, `sort`, ect.

Filters can be chained together using pipe command:
```sh
command1 | command2

# Output of command1 is input of command2
``` 

```sh
ls | sort -r

# Sorts the directory contents in reverse order
```

Shell variables are limited to the shell and accordingly, shells cannot see eachother's variables. To define a shell variables simply use `=` (without spaces around it)

```sh
# Define variable
var1="Hello"
var2="World"

# To view the variables's value
echo $var1
echo $var1 $var2

# To clear a variable
unset var2

# List all shell variables
set | head -4
```

Environment variables are just like shell variables except they have extended scope. They persist in any child processes spawned by the shell in which they originate. We can extend any shell variable to environment variable by applying `export` command to it.
```sh
# Extend scope
export var1

# List all environment variables
env
```

### 3.2. Useful Features of the Bash Shell

Metacharacters are special characters that have meaning to the shell.
- `#` include comments, which are ignored by the shell
- `;` separate commands typed on the same line
- `*` represent any number of consecutive characters within a filename pattern
- `?` acts as a single-character version of the asterisk metacharacter
  ```sh
  ls /bin/?ash
  # /bin/Bash   /bin/dash
  ```

Quoting is used to specify whether the shell should interpret special characters as metacharacters, or "escape" them.
- `\` escape interpretation of a single character as a metacharacter
  ```sh
  echo "\$1 each"
  # $1 each
  ```
- `" "` interpret text literally, except for any metacharacters, which will be interpreted according to their special meanings
  ```sh
  echo "\$1 each"
  #  each
  # Notice that the above command printed an extra space
  ```
- `' '` interpret all contents as literal characters
  ```sh
  echo "\$1 each"
  # &1 each
  ```

Input/Output, or I/O redirection refers to a set of features used for redirecting either the standard input, which is the keyboard, or the standard output, which is the terminal.
- `>` redirect standard output of a command to a file. It also creates the file if it doesn't exist, and overwrites its contents if it already exists
- `>>` avoid overwriting by appending output to any existing content
- `2>` redirects an error message to a file
- `2>>` append the error message
- `<` pass file contents as input to the standard input
  ```sh
  echo "line1" > eg.txt
  cat eg.txt
  # line1
  echo "line2" >> eg.txt
  cat eg.txt
  # line1
  # line2
  
  garbage
  # garbage: command not found
  garbage 2> err.txt
  cat err.txt
  # garbage: command not found
  ```

We can use command substitution when we want to replace a command with its output. There are two equivalent notations:
1. $(command)
2. 'command'

For example, let's say we want to store the `pwd` in a variable called "here": 
```sh
here=$(pwd)
echo $here
# /home/me
```

Command line arguments are arguments used by a program that are specified on the command line. In particular, they provide a way to pass arguments to a shell script, which is itself a program. Command line arguments for a Bash script are specified like this:
```sh
./MyBashScript.sh arg1 arg2
```
Here, the arguments "arg1" and "arg2" are passed to "MyBashScript.sh".

Bash has two main modes of operation:

1. Batch mode, which is the usual mode, runs commands sequentially.
  ```sh
  command1; command2
  # Command 2 only runs after command 1 is completed
  ```
1. In concurrent mode, commands run in parallel.
  ```sh
  command1 & command2
  # Command 2 only runs after command 1 is completed
  ```

### 3.3. Scheduling Jobs Using Cron

Cron is the general name of the tool that runs scheduled jobs consisting of shell commands or shell scripts. "Crond" is the daemon or service that interprets "crontab files" every minute and submits the corresponding jobs to cron at scheduled times. A "crontab", is a file containing jobs and schedule data. 

`crontab` is also a command that invokes a text editor to allow us to edit a crontab file.
```sh
# Open the default text editor
crontab -e
```
Using the editor, we can specify a new schedule and a command, which has the following syntax:
```
m h dom mon dow command
```

For example, the following syntax means: append the current date to the file "sundays.txt" at 15:30 every Sunday.

```sh
30 15 * * 0 date >> sundays.txt
```

```sh
# List cronjobs and their schedules
crontab -l | tail -6

# m h dom mon dow command
#
# 0 0 * * * /cron_scripts/load_data.sh
# 0 2 * * * /cron_scripts/backup_data.sh
```


<br>
<br>

****************
## 4. Examples

### 4.1. Example 1

Let's start with a commonly used example.

- `sort`: sorts lines in input
- `uniq`: prints input with consecutive repeated lines collapsed to a single, unique line

With the help of the pipe operator, we can combine these commands to print all the unique lines in a file!

Suppose we have the file pets.txt with the following contents:

```sh
cat pets.txt
# goldfish
# dog
# cat
# parrot
# dog
# goldfish
# goldfish
```
If we only use sort on pets.txt, we get:

```sh
sort pets.txt
# cat
# dog
# dog
# goldfish
# goldfish
# goldfish
# parrot
```
And if we only use uniq, we get:

```sh
uniq pets.txt
# goldfish
# dog
# cat
# parrot
# dog
# goldfish
```
But by combining the two commands in the correct order, we get back:

```sh
sort pets.txt | uniq
# cat
# dog
# goldfish
# parrot
```
which are the sorted, unique lines from pets.txt!

### 4.2. Example 2

Some commands, such as `tr`, only accept "standard input" as input (not strings or filenames):

- `tr` (translate) → replaces characters in input text.
  ```
  Syntax: tr [OPTIONS] [target characters] [replacement characters]
  In cases like this, we can use piping to apply the command to strings and file contents.
  ```

With strings, we could, for example, use `echo` in combination with `tr` to replace all vowels in a string with underscores, as follows:

```sh
$ echo "Linux and shell scripting are awesome\!" | tr "aeiou" "_"
# L_n_x _nd sh_ll scr_pt_ng _r_ _w_s_m_!
```
To perform the complement of the operation from the previous example, that is, to replace all consonants with an underscore, we can use the `-c` option like this:

```sh
$ echo "Linux and shell scripting are awesome\!" | tr -c "aeiou" "_"
# _i_u__a_____e______i__i___a_e_a_e_o_e_
```
With files, we could use `cat` in combination with `tr` to change all of the text to upper case as follows:

```sh
cat pets.txt | tr "[a-z]" "[A-Z]"
# GOLDFISH
# DOG
# CAT
# PARROT
# DOG
# GOLDFISH
# GOLDFISH
```

The possibilities are endless! For example:

```sh
sort pets.txt | uniq | tr "[a-z]" "[A-Z]"
# CAT
# DOG
# GOLDFISH
# PARROT
```

### 4.3. Example 3

We can even use `curl` in combination with the `grep` command to extract components of URL data by piping the output of curl to grep.
Let's see how we can use this pattern to get the current price of BTC (Bitcoin) in USD.

First, we find a public URL API. In this example, we will use one provided by CoinStats.

Specifically, they provide a public API (no key required) https://api.coinstats.app/public/v1/coins/bitcoin\?currency\=USD which returns some json about the current BTC price in USD.

Entering the following command returns the BTC price data, displayed as a json object:

```sh
curl -s --location --request GET https://api.coinstats.app/public/v1/coins/bitcoin\?currency\=USD

```

```json
{
  "coin": {
    "id": "bitcoin",
    "icon": "https://static.coinstats.app/coins/Bitcoin6l39t.png",
    "name": "Bitcoin",
    "symbol": "BTC",
    "rank": 1,
    "price": 57907.78008618953,
    "priceBtc": 1,
    "volume": 48430621052.9856,
    "marketCap": 1093175428640.1146,
    "availableSupply": 18877868,
    "totalSupply": 21000000,
    "priceChange1h": -0.19,
    "priceChange1d": -0.4,
    "priceChange1w": -9.36,
    "websiteUrl": "http://www.bitcoin.org",
    "twitterUrl": "https://twitter.com/bitcoin",
    "exp": [
      "https://blockchair.com/bitcoin/",
      "https://btc.com/",
      "https://btc.tokenview.com/"
    ]
  }
}
```

The json field we want to grab here is "price": [numbers].[numbers]". To grab this we can use the following `grep` command to extract it from the json text:

```sh
grep -oE "\"price\"\s*:\s*[0-9]*?\.[0-9]*"
```

- `-o` tells `grep` to only return the matching portion
- `-E` tells `grep` to be able to use extended regex symbols such as `?`
- `\"price\"` matches the string `"price`"
- `\s*` matches any number (including 0) of whitespace (`\s`) characters
- `:` matches `:`
- `[0-9]*` matches any number of digits (from 0 to 9)
- `?\.` optionally matches a `.` (this is in case price were an integer)

Now that we have the grep statement that we need, we can pipe the BTC data to it using the curl command from above:

```sh
$ curl -s --location --request GET https://api.coinstats.app/public/v1/coins/bitcoin\?currency\=USD |\ grep -oE "\"price\":\s*[0-9]*?\.[0-9]*"

# "price": 57907.78008618953
```
The backslash `\` character used here after the pipe `|` allows us to write the expression on multiple lines.

Finally, to get only the value in the price field, and drop the "price" label, we can use chaining to pipe the same output to another grep:

```sh
$ curl -s --location --request GET https://api.coinstats.app/public/v1/coins/bitcoin\?currency\=USD |\
    grep -oE "\"price\":\s*[0-9]*?\.[0-9]*" |\
    grep -oE "[0-9]*?\.[0-9]*"

# 57907.78008618953
```