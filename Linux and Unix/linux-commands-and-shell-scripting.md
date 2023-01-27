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