# Linux Upskill Challenge - Week 1

* [Website of the course](https://LinuxUpskillChallenge.org)
* [Full lesson source](https://github.com/livialima/linuxupskillchallenge)
* [Complementary video playlists](https://www.youtube.com/@livia2lima/search?query=linuxupskillchallenge)
* [Monthly lessons on Reddit](https://www.reddit.com/r/linuxupskillchallenge/)

* [Day 0](docs/00-AWS-Free-Tier.md) - **Creating Your Own Server**: how to setup your lab in [AWS](docs/00-AWS-Free-Tier.md), [Azure](docs/00-Azure-Free-Tier.md), [Google Cloud](docs/00-Google-Cloud.md), [Digital Ocean](docs/00-VPS-small.md) and [on a local server](docs/00-Local-Server.md)
* [Day 1](docs/01.md) - **Get to know your server**: Starting with `ssh`-ing in and some simple commands like: `ls`, `uptime`, `free`, `df -h`, `uname -a`. Extensions on doing passwordless login with public keys and and an `ssh` config file.
* [Day 2](docs/02.md) - **Basic navigation**: Basic navigation, the "man" pages, file hierarchy
* [Day 3](docs/03.md) - **Power trip!**: Working with `sudo`, `uptime`, `timezones`, changing your hostname
* [Day 4](docs/04.md) - **Installing software, exploring the file structure**: Using 'apt' to find and install software. Use of `mc` to explore the filesystem. Looking at the contents of: `/etc/passwd`, `/etc/ssh/sshd_config` and `/var/log/auth.log`
* [Day 5](docs/05.md) - **More or less...**: Using `more`, `less` and navigating in these. Dotfiles, history, tab completion, and using the `nano` txt editor

## Day 1 - Get to know your server

- Use `lsb_release -a` or `cat /etc/os-release` to see which Linux distro and version you’re using
- `uname -a` will also print the system information and it can show some interesting things like kernel version, hardware platform, etc.
- `uptime` will show you how long the system has been running. It kinda makes the weird numbers you get from `cat /proc/uptime` a lot more readable.
- `whoami` will print the user name you logged on with, who will show who is logged on and w will also show what they are doing.

```bash
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 22.04.3 LTS
Release:        22.04
Codename:       jammy
$ cat /etc/os-release
PRETTY_NAME="Ubuntu 22.04.3 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.3 LTS (Jammy Jellyfish)"
VERSION_CODENAME=jammy
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=jammy

$ uname -a
Linux ip-172-31-20-77 6.2.0-1017-aws #17~22.04.1-Ubuntu SMP Fri Nov 17 21:07:13 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux

$ uptime
 11:11:29 up 16:24,  1 user,  load average: 0.00, 0.00, 0.00
$ cat /proc/uptime
59082.67 58808.55

$ whoami
ubuntu
```

- `lshw` can give some detailed information on the hardware configuration
- `lscpu` to display information about the CPU architecture
- `lsblk` to list block devices
- `lspci` to list all PCI devices
- `lsusb` to list USB devices

<br>

- `free -h` or `vmstat` to check the amount of memory used in the system
- `top` is like a Task Manager for Linux, it will display the processes and the consumption of resources
- `htop` is an interactive, prettier version.

```bash
$ free -h
               total        used        free      shared  buff/cache   available
Mem:           949Mi       183Mi       240Mi       0.0Ki       525Mi       584Mi
Swap:             0B          0B          0B
$ vmstat
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0      0 246584  32144 505696    0    0    13    35   13   23  0  0 100  0  0
```

- use `df -h` to see disk space usage
- use `du -h` if you want to estimate the size of your folders

```bash
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root       7.6G  2.4G  5.3G  31% /
tmpfs           475M     0  475M   0% /dev/shm
tmpfs           190M  880K  190M   1% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
/dev/xvda15     105M  6.1M   99M   6% /boot/efi
tmpfs            95M  4.0K   95M   1% /run/user/1000
$ du -h
4.0K    ./.config/htop
4.0K    ./.config/procps
12K     ./.config
4.0K    ./.cache
8.0K    ./.ssh
44K     .
```

- use `ifconfig` or its modern substitute `ip address` for a general idea of your network interfaces and their IP addresses
- to show you bandwidth usage, use `netstat -i` in a more static view and `ifstat` in a continuous view
- if you want more info on that traffic, `sudo iftop -i eth0` is a nice display (change eth0 for the interface you wish to capture traffic information)

```bash
$ ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 172.31.20.77  netmask 255.255.240.0  broadcast 172.31.31.255
        inet6 fe80::f:e0ff:fee1:39a9  prefixlen 64  scopeid 0x20<link>
        ether 02:0f:e0:e1:39:a9  txqueuelen 1000  (Ethernet)
        RX packets 200942  bytes 258114234 (258.1 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 36421  bytes 4650898 (4.6 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 580  bytes 64934 (64.9 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 580  bytes 64934 (64.9 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

$ ip address
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc fq_codel state UP group default qlen 1000
    link/ether 02:0f:e0:e1:39:a9 brd ff:ff:ff:ff:ff:ff
    inet 172.31.20.77/20 metric 100 brd 172.31.31.255 scope global dynamic eth0
       valid_lft 3529sec preferred_lft 3529sec
    inet6 fe80::f:e0ff:fee1:39a9/64 scope link
       valid_lft forever preferred_lft forever

$ netstat -i
Kernel Interface table
Iface      MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
eth0      9001   201007      0      0 0         36486      0      0      0 BMRU
lo       65536      584      0      0 0           584      0      0      0 LRU
```

### Extension

The Linux Out-of-Memory (OOM) Killer is a component of the Linux kernel that kicks in when the system is critically low on memory. When available memory becomes so scarce that the system can no longer function properly, the OOM Killer selects and terminates one or more processes to free up memory and allow the system to continue operating.

## Day 2 - Basic navigation

RTFM - "read the f***ing manual"

- each application installed comes with its own page in manual which we can see with `man` command
- to see shorter explanation, use `tldr` 
- if you know a keyword or some description of what the command is supposed to do, you can try `apropos` or `man -k` like this:

```bash
$ apropos "create new"
newusers (8)         - update and create new users in batch
$ man -k "create new"
newusers (8)         - update and create new users in batch
```

- `builtin commands` are contained within the shell itself and they (mostly) don't have a manual that you can read with man
- the best way to know if a command is a `builtin command`, is to check its `type`
- if `man` does not work, we can use `help` to display information about them
- `info` reads the documentation stored in info format

```bash
$ man export
No manual entry for export
$ help export
export: export [-fn] [name[=value] ...] or export -p
    Set export attribute for shell variables.
...

$ type export
export is a shell builtin
$ type grep
grep is aliased to `grep --color=auto'
$ type touch
touch is /usr/bin/touch
```

- `/` is the “root” of a branching tree of folders (also known as directories)
- At all times you are “in” one part of the system - the command `pwd` (“print working directory”) will show you where you are
- Generally your prompt is also configured to give you at least some of this information, so if I’m “in” the /etc directory then the prompt might be `steve@202.203.203.22:/etc$` or simply `/etc: $`
<br>

- `cd` moves to different areas - so `cd /var/log` will take you into the `/var/log` folder
- You can move “up” the structure by typing `cd ..` 
- A “relative” location is based on your present working directory - e.g. if you are “in” `/var`, and you can move to `/var/log` in two ways - either by providing the full path with `cd /var/log` or simply the “relative” path with the command `cd log`
- A simple `cd` is equal to `cd ~` and will always return you to your own defined “home directory”, also referred to as `~` (the “tilde” character) [NB: this differs from DOS/Windows]
- `cd ./directory` - change to a directory relative to the current directory
- `cd ../directory` - move up one level and then into a directory relative to the parent directory
- `cd -` moves you to the last visited (previous) directory
<br>

- What files are in a folder? The `ls` (list) command will give you a list of the files, and sub folders. Like many Linux commands, there are options (known as “switches”) to alter the meaning of the command or the output format.
- By convention, files with a starting character of “.” are considered hidden and we need to use `ls` with the `-a` switch to include them
- Generally most Linux command will accept one or more “parameters”, and one or more “switches”. Many commands accept a large number of switches, and these can generally be combined (use `ls -ltra`, rather than `ls -l -t -r -a`)
- In your home directory type `ls -ltra` and look at the far left hand column - those entries with a “d” as the first character on the line are directories (folders) rather than files. They may also be shown in a different color or font - if not, then adding the “–color=auto” switch should do this (i.e. `ls -ltra --color=auto`)
<br>

- You can make a new folder/directory with the `mkdir` command
- use `rmdir` to remove empty directory
- use `rm -r` to remove non-empty directory
- use `rm` to remove a file
- You can make new empty files with the `touch` command
- When you want to move file or directory to another directory, you use `mv` and specify the path to move
<br>

- Learn about `pushd` and `popd` to navigate around multiple directories easily. Running `pushd /var/log` moves you to to the `/var/log`, but keeps track of where you were. You can `pushd` more than one directory at a time. Try it out: `pushd /var/log`, `pushd /dev`, `pushd /etc`, `pushd`, `popd`, `popd`. Note how `pushd` with no arguments switches between the last two _pushed_ directories but [more complex navigation is also possible](https://opensource.com/article/19/8/navigating-bash-shell-pushd-popd). 

## Day 3 - Power trip!

- **Global**: programs/environments that any user can use, used across the system. A global change affects all users. 
- **Local** or **By user**: programs/environments that a particular user runs, not available to other users. A local change affects only one user.

3 types of users in a Linux system:
- `root` - the powerful superuser that can execute any command at any level in the system. They can do all global changes as well as local changes for any user.
- `sudoers` - regular users that are allowed to use `sudo`, i.e., they can execute commands in one or more levels in the system, can do some or all global changes. It’s common to have at least one sudoer that has the same powers as root, but the amount of priviledges other sudoers have can vary.
- `regular users` - users that can use the system but can only do local changes, i.e., can only deal with their own files/directories and environment variables.

Root is the power user on a Linux system. A typo in a command could potentially cripple your server. It’s now common Best Practice to discourage or disallow login directly by `root` and instead to give specified trusted users the permission to run root-only commands via the `sudo` command.

Stop using root and create a regular user and adding it to a sudoers group. Adding a regular user to a group with `sudo` priviledges is the easiest way to do it, as the `sudo` group is pretty standard in Ubuntu. But this can also be accomplished by modifying the `/etc/sudoers` using the command `visudo`. Login with this new user from now on.

```bash
$ sudo adduser adin
$ sudo usermod -a -G sudo adin
$ getent group|grep adin
sudo:x:27:ubuntu,adin
adin:x:1001:
```

- Use the `passwd` command to change your password
- In a production system, public keys and/or two factor authentication would be more appropriate than password.
- `/etc/shadow` file is where the hashed passwords are kept. It is a prime target for intruders who aim to grab it and use offline password crackers to discover the passwords.
- Test running the `reboot` command, and then via `sudo` (i.e. `sudo reboot`)
- Use the `uptime` command to confirm that your server did actually fully restart
<br>

- use command `sudo -i` to fully “becoming root” which can be handy if you have a series of commands to do “as root”
- `sudo -i` stands for “sudo interactive” and it launches a new login shell for the root user. This means that it creates a new environment for the root user with the root user’s home directory and shell configuration files. This makes it similar to logging in directly as the root user, and any commands executed from this shell will have the privileges of the root user. `sudo -s` stands for “sudo shell” and it launches a new shell for the root user, but it does not create a new login shell. This means that it does not change the environment or shell configuration files of the current user. Any commands executed from this shell will have the privileges of the root user, but the environment will still be that of the current user.
- Type `exit` or `logout` to get back to your own normal “support” login.
- Use `less` to view the file `/var/log/auth.log`, where any use of `sudo` is logged
- You could “filter” this by typing: `grep "sudo" /var/log/auth.log`
<br>

- `sudo hostnamectl set-hostname mylittlecloudbox` - to rename your server
- `timedatectl` to check the current setting
- `timedatectl list-timezones` to get a list of available timezones
- `sudo timedatectl set-timezone Australia/Sydney` to set a timezone

### Task

- Change the password of your `sudo` user
Switch to user and use `passwd` command

- Change the hostname
`$ sudo hostnamectl set-hostname linuxupskillchallenge`

- Change the timezone
```bash
$ timedatectl
               Local time: Tue 2024-02-27 17:29:59 UTC
           Universal time: Tue 2024-02-27 17:29:59 UTC
                 RTC time: Tue 2024-02-27 17:29:59
                Time zone: Etc/UTC (UTC, +0000)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
ubuntu@linuxupskillchallenge:~$ sudo timedatectl set-timezone Europe/Sarajevo
ubuntu@linuxupskillchallenge:~$ timedatectl
               Local time: Tue 2024-02-27 18:30:22 CET
           Universal time: Tue 2024-02-27 17:30:22 UTC
                 RTC time: Tue 2024-02-27 17:30:22
                Time zone: Europe/Sarajevo (CET, +0100)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

### Extension

- `visudo` is a command-line utility used to edit and manage the `/etc/sudoers` file. This file controls who can run commands with elevated privileges (as root) using the `sudo` command. When you run `visudo`, it opens the `/etc/sudoers` file in a text editor specified by the `EDITOR` environment variable (typically `vi` or `vim`, hence the name "visudo"). It ensures that only one user can edit the file at a time and performs syntax checking to prevent errors that could potentially lock you out of the system.
- SSH is essential to server management. The settings file for OpenSSH on Ubuntu 16.04 is located at /etc/ssh/sshd_config. You will need to be root or use sudo to edit and control the SSH server. It is always a good idea to make a backup of any configuration files before editing them: `cp /etc/ssh/sshd_config /etc/ssh/backup.sshd_config`. After editing the configuration file you should test that it is valid before reloading the service: `sshd -t`. Once you think your edits are good, reload the SSH daemon: `sudo systemctl reload sshd`. 
- SSH tunneling or SSH port forwarding is a method of creating an encrypted SSH connection between a client and a server machine through which services ports can be relayed. SSH forwarding is useful for transporting network data of services that use an unencrypted protocol, such as VNC or FTP , accessing geo-restricted content, or bypassing intermediate firewalls. Basically, you can forward any TCP port and tunnel the traffic over a secure SSH connection.

## Day 4 - Installing software, exploring the file structure

### Task

- Install a new application from the online repositories
- Become familiar with some of the standard directories
- Look at the format and content of some configuration files.

As long as we know what the name or description of a package (=app) is, then we can search for it and we can then install them with `apt install` command:

```bash
$ apt search "midnight commander"
Sorting... Done
Full Text Search... Done
avfs/jammy 1.1.4-2 amd64
  virtual filesystem to access archives, disk images, remote locations

gnome-commander/jammy 1.14.2-1 amd64
  nice and fast file manager for the GNOME desktop

gnome-commander-data/jammy 1.14.2-1 all
  Data files for GNOME Commander

junior-system/jammy 1.30ubuntu1 all
  Debian Jr. System tools

krusader/jammy 2:2.7.2-2 amd64
  twin-panel (commander-style) file manager

mc/jammy 3:4.8.27-1 amd64
  Midnight Commander - a powerful file manager

mc-data/jammy 3:4.8.27-1 all
  Midnight Commander - a powerful file manager -- data files

moc/jammy 1:2.6.0~svn-r3005-1 amd64
  ncurses based console audio player

pilot/jammy 2.25+dfsg1-1build1 amd64
  Simple file browser from Alpine, a text-based email client

$ sudo apt install mc

$ mc
```

The Linux Directory Structure:
- / -- The Root Directory
- /bin -- Essential User Binaries
- /boot -- Static Boot Files
- /cdrom -- Historical Mount Point for CD-ROMs
- /dev -- Device Files
- /etc -- Configuration Files
- /home -- Home Folders
- /lib -- Essential Shared Libraries
- /lost+found -- Recovered Files
- /media -- Removable Media
- /mnt -- Temporary Mount Points
- /opt -- Optional Packages
- /proc -- Kernel & Process Files
- /root -- Root Home Directory
- /run -- Application State Files
- /sbin -- System Administration Binaries
- /selinux -- SELinux Virtual File System
- /srv -- Service Data
- /tmp -- Temporary Files
- /usr -- User Binaries & Read-Only Data
- /var -- Variable Data Files

```bash
DESCRIPTION
       /etc   Contains configuration files which are local to the machine.  Some larger software packages, like  X11,
              can  have  their own subdirectories below /etc.  Site-wide configuration files may be placed here or in
              /usr/etc.  Nevertheless, programs should always look for these files in /etc and you may have links for
              these files to /usr/etc.

       /home  On machines with home directories for users, these are usually beneath this directory, directly or not.
              The structure of this directory depends on local administration decisions (optional).

       /lib   This  directory should hold those shared libraries that are necessary to boot the system and to run the
              commands in the root filesystem.
       /mnt   This directory is a mount point for a temporarily mounted filesystem.  In some distributions, /mnt con‐
              tains subdirectories intended to be used as mount points for several temporary filesystems.

       /opt   This directory should contain add-on packages that contain static files.

       /root  This directory is usually the home directory for the root user (optional).

       /run   This  directory contains information which describes the system since it was booted.  Once this purpose
              was served by /var/run and programs may continue to use it.

       /sbin  Like /bin, this directory holds commands needed to boot the system, but which are usually not  executed
              by normal users.

       /usr/bin
              This  is  the  primary directory for executable programs.  Most programs executed by normal users which
              are not needed for booting or for repairing the system and which are not installed  locally  should  be
              placed in this directory.

       /usr/lib
              Object  libraries, including dynamic libraries, plus some executables which usually are not invoked di‐
              rectly.  More complicated programs may have whole subdirectories there.

       /var/log
              Miscellaneous log files.
```

- Some interesting files to look at are: `/etc/passwd`, `/etc/ssh/sshd_config` and `/var/log/auth.log`

- Search for “hangman”, install and play a couple of rounds…

```bash
$ apt search hangman
Sorting... Done
Full Text Search... Done
bsdgames/jammy 2.17-29 amd64
  collection of classic textual unix games

kdeedu-kvtml-data/jammy 4:21.12.3-0ubuntu1 all
  KVTML files for KDE-Edu programs

khangman/jammy 4:21.12.3-0ubuntu1 amd64
  Hangman word puzzle

libkeduvocdocument-data/jammy 4:21.12.3-0ubuntu1 all
  library for reading and writing vocabulary files - data files

libkeduvocdocument5abi1/jammy 4:21.12.3-0ubuntu1 amd64
  library for reading and writing vocabulary files

$ sudo apt install bsdgames

$ hangman 

     ______
     |    |
     |    O  
     |    |
     |    |  
     |       
   __|_____  
   |      |___
   |_________|

 Word:  interfacing
Guess:
You got it!
Another word?
```

### Extension

- apt consists of some of the most widely used features from apt-get and apt-cache, leaving aside obscure and seldom-used features. It can also manage apt.conf file. With apt, you don’t have to fiddle your way from apt-get commands to apt-cache. apt is more structured and provides the user with the necessary options needed to manage packages. The main goal of apt is to provide an efficient way of handling packages in a way “pleasant for end users”.

## Day 5 - More or less…

### Task

Use tab completion
- “tab completion” is a handy feature that helps you enter commands correctly. It helps find both the command and also file name parameters.

Search in the command history
- press the “Up arrow” to scroll back through the commands
- `history` command lists out the whole of your cached command history
- repeat a command - pick one line to repeat (say number 20) and repeat it by typing !20 and pressing “Enter” (very handy when typing long, complex commands)
- press `Ctrl + r`, then start typing any part of the command that you are looking for. You’ll see an autocomplete of a past command at your prompt. If you keep typing, you’ll get more specific options appear. You can either run it by pressing return, or editing it first by pressing arrows or other movement keys. You can also keep pressing `Ctrl + r` to see other instances of the same command you used with different options.

Read a dot file using more and less
- in Linux, any file starting with a “.” character is hidden
- `more` command is used to view the text files in the command prompt, displaying one screen at a time in case the file is large
- we can use `less` to look at the contents of `.bashrc` and other files
- `less` command is an improved version of `more` with additional features

`less` Search
- / – search for a pattern which will take you to the next occurrence
- ? – search for a pattern which will take you to the previous occurrence
- n – for next match in forward
- N – for previous match in backward
- CTRL+F – forward one window
- CTRL+B – backward one window
- CTRL+D – forward half window
- CTRL+U – backward half window
- j – navigate forward by one line
- k – navigate backward by one line
- G – go to the end of file
- g – go to the start of file
- q or ZZ – exit the less pager
- You can open multiple files by passing the file names as arguments: `$ less file1 file2`
- While you are viewing file1, use :e to open the file2: `$ less file1; :e file2`
- :n – go to the next file.
- :p – go to the previous file.

Change / customize your prompt
Finally, use the `nano` editor to create a file in your home directory and type up a summary of how the last five days have worked for you.
- nano is a simple and easy-to-use text editor in Unix/Linux systems
- open a file in Nano: `nano filename.txt`

## Extension

We’re using `bash` as our terminal shell for now (it is standard in many distros) but it is not the only one out there

1. **Bash (Bourne Again Shell)**:   
    - Bash is the default shell on many Unix/Linux systems.
    - It's known for its wide compatibility, robust scripting capabilities, and extensive use in system administration and scripting tasks.
    - Bash is powerful and versatile, but it lacks some modern features found in newer shells like Fish and Zsh.

2. **Zsh (Z Shell)**:
    - Zsh is an extended version of the Bourne shell with many enhancements and additional features.
    - It offers advanced features such as advanced tab completion, spelling correction, syntax highlighting, and powerful customization options.
    - Zsh is often chosen by power users and developers for its flexibility and advanced capabilities.

3. **Fish (Friendly Interactive Shell)**:
    - Fish is designed to be user-friendly and intuitive, with a focus on simplicity and discoverability.
    - It features syntax highlighting, auto-suggestions, and extensive tab completion out of the box, making it very user-friendly, especially for beginners.
    - Fish prioritizes ease of use for interactive shell sessions but may have less powerful scripting capabilities compared to Bash and Zsh.

4. **Oh My Zsh**:
    - Oh My Zsh is a framework for managing Zsh configurations, providing a wide range of plugins, themes, and tools to enhance the Zsh shell's functionality and aesthetics.
    - It offers features like a plugin system for enabling or disabling additional functionalities, customizable themes, enhanced auto-completion, and easy installation and management of configurations.
    - Oh My Zsh builds on top of Zsh, making it a popular choice for users who want to maximize their productivity and improve their terminal experience.

TMUX is a terminal multiplexer, which means it's a software application that enables you to use multiple terminal sessions within a single terminal window or terminal emulator. Here's a simple explanation of what TMUX does and why it's useful:
- Multiple Windows and Panes: TMUX allows you to create multiple virtual terminal windows and split them into multiple panes. This means you can work on multiple tasks simultaneously within the same terminal window, without needing to open multiple terminal windows or tabs.
- Session Management: TMUX lets you create and manage sessions, which are collections of one or more terminal windows with their associated processes. You can detach from and reattach to sessions, allowing you to disconnect from your work and later resume exactly where you left off.
- Customization: TMUX is highly customizable, allowing you to configure key bindings, create custom layouts, and personalize the appearance of your terminal sessions to suit your preferences and workflow.
- Remote Access: TMUX is especially useful for remote server administration or remote pair programming. You can start a TMUX session on a remote server, detach from it, and later reattach from a different location, ensuring that your work remains uninterrupted.