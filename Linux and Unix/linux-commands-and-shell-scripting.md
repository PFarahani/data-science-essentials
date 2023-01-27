<img src="assets/linux-logo.svg" width="250" height="250">


# Hands-on Introduction to Linux Commands and Shell Scripting  <!-- omit from toc -->

## Table of contents <!-- omit from toc -->

- [1. Introduction to Linux](#1-introduction-to-linux)
  - [1.1. Overview of Linux Architecture](#11-overview-of-linux-architecture)
  - [1.2. Creating and Editing Texts](#12-creating-and-editing-texts)
- [2. Linux Commands](#2-linux-commands)


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
