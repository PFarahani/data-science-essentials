# Command Line Interface (CLI) Cheat Sheet <!-- omit from toc -->

## Table of Contents <!-- omit from toc -->
- [Directories](#directories)
- [Output](#output)
- [Files](#files)
- [Permissions](#permissions)
- [Search](#search)
- [Network](#network)
- [Processes](#processes)
- [Getting Help](#getting-help)
- [File Permissions](#file-permissions)
- [Combining Commands](#combining-commands)
- [Home Folder](#home-folder)
- [Output with `less`](#output-with-less)
- [Directing Output](#directing-output)
- [The `CTRL` Key](#the-ctrl-key)
- [The `TAB` Key](#the-tab-key)
- [The Arrow Keys](#the-arrow-keys)


## Directories

- **Display path of current working directory**
  ```sh
  $ pwd
  ```

- **Change directory to `<directory>`**
  ```sh
  $ cd <directory>
  ```

- **Navigate to parent directory**
  ```sh
  $ cd ..
  ```

- **List directory contents**
  ```sh
  $ ls
  ```

- **List detailed directory contents, including hidden files**
  ```sh
  $ ls -la
  ```

- **Create new directory named `<directory>`**
  ```sh
  $ mkdir <directory>
  ```

## Output

- **Output the contents of `<file>`**
  ```sh
  $ cat <file>
  ```

- **Output the contents of `<file>` using the less command**
  ```sh
  $ less <file>
  ```

- **Output the first 10 lines of `<file>`**
  ```sh
  $ head <file>
  ```

- **Direct the output of `<cmd>` into `<file>`**
  ```sh
  $ <cmd> > <file>
  ```

- **Append the output of `<cmd>` to `<file>`**
  ```sh
  $ <cmd> >> <file>
  ```

- **Direct the output of `<cmd1>` to `<cmd2>`**
  ```sh
  $ <cmd1> | <cmd2>
  ```

- **Clear the command line window**
  ```sh
  $ clear
  ```

## Files

- **Delete `<file>`**
  ```sh
  $ rm <file>
  ```

- **Delete `<directory>`**
  ```sh
  $ rm -r <directory>
  ```

- **Force-delete `<file>`**
  ```sh
  $ rm -f <file>
  ```

- **Rename `<file-old>` to `<file-new>`**
  ```sh
  $ mv <file-old> <file-new>
  ```

- **Move `<file>` to `<directory>`**
  ```sh
  $ mv <file> <directory>
  ```

- **Copy `<file>` to `<directory>`**
  ```sh
  $ cp <file> <directory>
  ```

- **Copy `<directory1>` and its contents to `<directory2>`**
  ```sh
  $ cp -r <directory1> <directory2>
  ```

- **Update file access & modification time (and create `<file>` if it doesn’t exist)**
  ```sh
  $ touch <file>
  ```

## Permissions

- **Change permissions of `<file>` to 755**
  ```sh
  $ chmod 755 <file>
  ```

- **Change permissions of `<directory>` (and its contents) to 600**
  ```sh
  $ chmod -R 600 <directory>
  ```

- **Change ownership of `<file>` to `<user>` and `<group>`**
  ```sh
  $ chown <user>:<group> <file>
  ```

## Search

- **Find all files named `<file>` inside `<dir>`**
  ```sh
  $ find <dir> -name "<file>"
  ```

- **Output all occurrences of `<text>` inside `<file>`**
  ```sh
  $ grep "<text>" <file>
  ```

- **Search for all files containing `<text>` inside `<dir>`**
  ```sh
  $ grep -rl "<text>" <dir>
  ```

## Network

- **Ping `<host>` and display status**
  ```sh
  $ ping <host>
  ```

- **Output whois information for `<domain>`**
  ```sh
  $ whois <domain>
  ```

- **Download `<file>` (via HTTP[S] or FTP)**
  ```sh
  $ curl -O <url/to/file>
  ```

- **Establish an SSH connection to `<host>` with user `<username>`**
  ```sh
  $ ssh <username>@<host>
  ```

- **Copy `<file>` to a remote `<host>`**
  ```sh
  $ scp <file> <user>@<host>:/remote/path
  ```

## Processes

- **Output currently running processes**
  ```sh
  $ ps ax
  ```

- **Display live information about currently running processes**
  ```sh
  $ top
  ```

- **Quit process with ID `<pid>`**
  ```sh
  $ kill <pid>
  ```

## Getting Help

- **To receive detailed documentation about the command in question**
  ```sh
  $ man <command>
  $ <command> --help
  ```

## File Permissions

- **On Unix systems, file permissions are set using three digits**
  - **4 — access/read (r)**
  - **2 — modify/write (w)**
  - **1 — execute (x)**

For example:
- `755` means “rwx” for owner and “rx” for both group and anyone.
- `740` represents “rwx” for owner, “r” for group, and no rights for other users.

## Combining Commands

- **Run a series of commands after another by separating them with a semicolon `;`**
  ```sh
  $ cd ~/videos || mkdir ~/videos
  ```

- **Execute a command only if its predecessor produces a certain result using `&&` and `||` operators**
  ```sh
  $ cd ~/videos || mkdir ~/videos
  ```

## Home Folder

- **Use `~` to address a path inside your home folder**
  ```sh
  $ cd ~/projects/
  ```

- **Reminder of your user name**
  ```sh
  $ whoami
  ```

## Output with `less`

- **The `less` command displays and paginates output**
  - **Scroll one page forward with SPACE**
  - **Scroll one page backward with `b`**
  - **Quit the `less` program with `q`**

## Directing Output

- **Direct output to a file using `>`**
  ```sh
  $ ps ax > ~/processes.txt
  ```

- **Pass output to another command using the `|` (pipe) operator**
  ```sh
  $ ls | grep ".pdf" | less
  ```

## The `CTRL` Key

- **Keyboard shortcuts for text entry**
  - **`CTRL+A` moves the caret to the beginning**
  - **`CTRL+E` moves the caret to the end**
  - **`CTRL+K` deletes all characters after**
  - **`CTRL+U` deletes all characters in front of the caret**
  - **`CTRL+L` clears the screen**
  - **`CTRL+C` cancels a running command**

## The `TAB` Key

- **Autocompletes paths and file names**
  ```sh
  $ cd ~/pr[TAB]ojects/ac[TAB]medesign/d[TAB]ocs/
  ```

- **View all possible matches with `TAB` twice if ambiguous**

## The Arrow Keys

- **Navigate command history with the `ARROW UP` and `ARROW DOWN` keys**
- **Print a list of all recent commands**
  ```sh
  $ history
  ```