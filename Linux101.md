# Linux 101

## Table of Contents

1. [Why is Linux Important for Cyber Defense](#why-is-linux-important-for-cyber-defense)
2. [Command Line Introduction](#command-line-introduction)
    1. [How To Get Help](#how-to-get-help)
    2. [Moving Around](#moving-around)
    3. [File Manipulation](#file-manipulation)
    4. [Hidden Files](#hidden-files)
3. [User Privileges](#user-privileges)
    1. [What About Admin?](#what-about-admin)
4. [Processes](#processes)
    1. [What is a Process?](#what-is-a-process)
    2. [What Processes Are Running?](#what-processes-are-running)
    3. [How Do I Kill a Process?](#how-do-i-kill-a-process)
5. [Filesystem Hierarchy](#filesystem-hierarchy)
    1. [Important System Configuration
       Files](#important-system-configuration-files)
    2. [Important User Configuration
       Files](#important-user-configuration-files)

## Why is Linux Important for Cyber Defense?

Estimates vary, but Linux is used to power the majority of the internet. Chances
are that if you become a cyber defense professional, you'll at least need to
have some familiarity with Linux.

## Command Line Introduction

### How To Get help

* `man` opens the manual page. For example: `man ls`

### Moving Around

* At any given time when you're using the terminal, you have a current working
    directory. `pwd` prints the absolute path of your working directory

* To change your current working directory, you can use `cd`. Let's go over the
    options we can use for this command. To see what files are in these
    directories, you can use `ls`.

    * `cd file/path` will change your current working directory to the directory
        specified.

    * `cd ~` and `cd` with no arguments will take you back to your home
        directory. You can use `~` anytime you want to refer to your home
        directory.

    * `cd /` will take you to your root directory. You can use `/` anytime you
        want to refer to your root directory.

    * `cd ..` will take you to the parent directory of your current working
        directory.

    * `cd -` will take you to the previous working directory.

### File Manipulation

* You can use `touch` to create a file. For example, `touch test`
* To delete that file, use `rm test`
* You can also use a text editor like `nano` to open a file.
* To change the name of a file, use `mv`
* To copy a file, use `cp`
* To make a directory, use `mkdir`
* To remove an empty directory, you can use `rmdir`. If the directory has files
    in it though, you need to use `rm -rf` to remove that directory and its
    contents
* To view a file, use `cat`, or `less` if you want to look at the file one page
    at a time

### Hidden Files

Files and directories that begin with `.` are not seen when you use `ls`. To see
these files, use `ls -a`.

## User Privileges

Many Linux distributions were created with the idea of having multiple users
connected to the same machine at the same time. In order to accomplish this,
Linux has users and groups that can each access some subset of the system's
files and programs.

To start, let's use `ls -al /home` to look at the permissions.

```
drwxr-xr-x  3 root root 4096 Dec 30  2018 ./
drwxr-xr-x 23 root root 4096 Jun 30 06:22 ../
drwxr-xr-x 11 rs   rs   4096 Sep 20 16:52 rs/
```

Let's pick apart the bottom line to see what's going on. The first character,
`d`, shows us that this file is a directory. If it was just a normal file, we
would see `-` here. The next 3 characters, `rwx` tell us that the *owner* can
read (r), write (w), and execute (x) the file. The next 3 characters, `r-x`
tell us that the *group* can read and execute, but not write to the file. The
last 3 characters in this sequence, `r-x` tell us that all other users can read
and execute.

With directories, the idea of reading, writing to, and executing a file are a
little different than for normal files. If a user can 'read' a directory, it
means they can `ls` inside that directory. Write permissions for a directory
allow a user to change a directory and its contents. If a user can 'execute' a
directory, it means they can enter that directory.

Also here, we see `root root` or `rs rs`. The first word in each of these is the
owner of the file, and the second word is the group that owns the file.

### What About Admin?

Linux doesn't have an Administrator account like Windows does, but it does have
a root user. The root user has access to all files and programs. You're probably
thinking that sounds pretty dangerous, and you're right. You should not use root
as your everyday user. Instead, you want to use a user that is able to run
commands as the root user. If you have a user like this, you can put `sudo` in
front of a command to elevate privileges to the root user. Later we'll look at
how we can give users this ability.

## Processes

### What is a Process?

A process carries out some task in the operating system. Processes are not
programs. Programs are a set of instructions, whereas a process is the actual
execution of those instructions.

### What Processes Are Running?

The most traditional way to view running processes is `top`. This will list the
processes and will put the processes using the most resources at the top.

Another utility is `ps`. There are many options that you can use for this
program, but `ps faux` is great for showing a comprehensive list of processes
and how they branch off from each other. You can also use `pstree` to view
processes.

### How Do I Kill a Process?

If a process is misbehaving, you'll need to find the PID and use `kill -9 PID`
to kill the process.

## Filesystem Hierarchy

In a competition, you want to know where certain things are stored, like program
binaries, system configuration files, and user configuration files.

* `/` is the root directory, where the directory tree starts.
* `/bin` contains executable programs that are needed to boot or repair the
    system
* `/sbin` contains commands needed to boot the system, but are not normally
    executed by normal users
* `/usr/bin` is the main directory for executable programs for normal users that
    aren't needed for booting or repairing the system
* `/usr/sbin` contains programs for system administration that are not needed
    for the boot process
* `/etc` contains system configuration files
* `~` for any given user contains that user's configuration files
* `/root` is distinct from `/`. `/root` is the root user's home directory on
    most distributions

### Important System Configuration Files

System configuration files are stored in `/etc`. Let's look through this folder
and see which ones we should be careful of during a competition. This list is
**NOT** comprehensive and filepaths may differ depending on distribution.

* Anything with cron in the name is going to be the configuration for scheduled
    processes. As you can imagine, an attacker could schedule a malicious
    command, so be aware of these files
* `/etc/group` lists the groups and who belongs to each group. If you see a user
    in the sudo or wheel group that you don't have control over during a
    competition, you should remove that user from that group
* `/etc/os-release` will give you information about what distribution you're
    using, which is helpful if you're searching for solutions to a problem
* `/etc/passwd` lists users and other information about those users
* `/etc/shadow` lists other, more sensitive information about users, including
    password hashes
* `/etc/skel/` is a folder that contains the default file structure for a new
    user. An attacker may add malicious files here so that any new users are
    already compromised
* `/etc/sudoers` and `/etc/sudoers.d/` are the configuration for which users
    are able to sudo. You should ***NEVER*** edit `/etc/sudoers` by hand. You
    should use `visudo` instead as it checks the files for errors before saving
* `/etc/ssh/sshd_config` is the configuration file for ssh. This describes how
    other people can connect to you. An attacker might be using ssh keys to
    connect to your machine, and here you can disable public key authentication
* Be aware of what package manager you use, and check its configuration files 

### Important User Configuration Files

User configuration files are usually hidden files in that user's home directory.
Again, this list is not exhaustive and filepaths may vary.

* `~/.bashrc`, `~/.bash_profile` and `~/.profile` contain lists of instructions that
    are executed whenever a user opens a terminal
* `~/.ssh/` contains a user's ssh configuration, including a list of authorized
    ssh keys that may be used to log into that user's account
* `~/.config/` contains even more user configuration files! Usually this is used
    for application configuration files, but you should still check this
