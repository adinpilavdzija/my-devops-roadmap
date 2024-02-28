# Linux Upskill Challenge

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

- `cd` moves to different areas - so `cd /var/log` will take you into the `/var/log` folder
- You can move “up” the structure by typing `cd ..` 
- A “relative” location is based on your present working directory - e.g. if you are “in” `/var`, and you can move to `/var/log` in two ways - either by providing the full path with `cd /var/log` or simply the “relative” path with the command `cd log`
- A simple `cd` is equal to `cd ~` and will always return you to your own defined “home directory”, also referred to as `~` (the “tilde” character) [NB: this differs from DOS/Windows]
- `cd ./directory` - change to a directory relative to the current directory
- `cd ../directory` - move up one level and then into a directory relative to the parent directory
- `cd -` moves you to the last visited (previous) directory

- What files are in a folder? The `ls` (list) command will give you a list of the files, and sub folders. Like many Linux commands, there are options (known as “switches”) to alter the meaning of the command or the output format.
- By convention, files with a starting character of “.” are considered hidden and we need to use `ls` with the `-a` switch to include them
- Generally most Linux command will accept one or more “parameters”, and one or more “switches”. Many commands accept a large number of switches, and these can generally be combined (use `ls -ltra`, rather than `ls -l -t -r -a`)
- In your home directory type `ls -ltra` and look at the far left hand column - those entries with a “d” as the first character on the line are directories (folders) rather than files. They may also be shown in a different color or font - if not, then adding the “–color=auto” switch should do this (i.e. `ls -ltra --color=auto`)

- You can make a new folder/directory with the `mkdir` command
- To delete (or remove) a directory, use `rmdir` if the directory is empty or `rm -r` if there still any files or other directories inside of it.
- You can make new empty files with the `touch` command
- When you want to move file or directory to another directory, you use `mv` and specify the path to move.
- To delete (or remove) a file, use `rm`.

- Learn about `pushd` and `popd` to navigate around multiple directories easily. Running `pushd /var/log` moves you to to the `/var/log`, but keeps track of where you were. You can `pushd` more than one directory at a time. Try it out: `pushd /var/log`, `pushd /dev`, `pushd /etc`, `pushd`, `popd`, `popd`. Note how `pushd` with no arguments switches between the last two _pushed_ directories but [more complex navigation is also possible](https://opensource.com/article/19/8/navigating-bash-shell-pushd-popd). 


## Day 3 - Power trip!

### TASK

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
---

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

- command `sudo -i` to fully “becoming root” which can be handy if you have a series of commands to do “as root”
- `sudo -i` stands for “sudo interactive” and it launches a new login shell for the root user. This means that it creates a new environment for the root user with the root user’s home directory and shell configuration files. This makes it similar to logging in directly as the root user, and any commands executed from this shell will have the privileges of the root user. `sudo -s` stands for “sudo shell” and it launches a new shell for the root user, but it does not create a new login shell. This means that it does not change the environment or shell configuration files of the current user. Any commands executed from this shell will have the privileges of the root user, but the environment will still be that of the current user.

- Type `exit` or `logout` to get back to your own normal “support” login.
- Use `less` to view the file `/var/log/auth.log`, where any use of `sudo` is logged
- You could “filter” this by typing: `grep "sudo" /var/log/auth.log`

- `sudo hostnamectl set-hostname mylittlecloudbox` - to rename your server
- `timedatectl` to check the current setting
- `timedatectl list-timezones` to get a list of available timezones
- `sudo timedatectl set-timezone Australia/Sydney` to set a timezone