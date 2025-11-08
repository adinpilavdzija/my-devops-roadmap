# Linux Upskill Challenge - Week 3

* [Day 11](docs/11.md) - **Finding things...**: Finding things with: `locate`, `find`, `grep`, `which`
* [Day 12](docs/12.md) - **Transferring files**: SFTP, the technology, clients, and copying up and down
* [Day 13](docs/13.md) - **Who has permission?**: Permissions, users, groups, (ACLS and SELinux in the Extension)
* [Day 14](docs/14.md) - **Users and Groups**: Using `adduser`, `visudo` to setup up a restricted "helper" to manage our host
* [Day 15](docs/15.md) - **Deeper into repositories...**: Repositories in more detail, how to enable "Multiverse", the role of PPAs in Ubuntu, enabling and installing from them

## Day 11 - Finding things…

- `Locate`: If you’re looking for a file called `access.log` use `locate access.log`. It’s very fast because it searches an index, but if this index is out of date or missing it may not give you the answer you’re looking for. This is because the index is created by the `updatedb` command - typically run only nightly by `cron`. It may therefore be out of date for recently added files, so it can be worthwhile updating the index by manually running: `sudo updatedb`.
```bash
$ locate access.log
/var/log/apache2/access.log
/var/log/apache2/access.log.1
/var/log/apache2/access.log.2.gz` 
```

- `find` command searches down through a directory structure looking for files which match some criteria - which could be name, but also size, or when last updated etc. 
- `find /var -name access.log` - searches for files with the name “access.log”
- `find /home -mtime -3` - searches for any file under `/home` with a last-modified date in the last 3 days
- `find` commands take longer than `locate` did because they search through the filesystem directly rather from an index
- `find` uses the permissions of the logged-in user, so you’ll get “permission denied” messages for many directories if you search the whole system
- Starting the command with `sudo` of course will run it as root (or you could filter the errors with `grep` like this: `find /var -name access.log 2>&1 | grep -vi "Permission denied"`)

- `grep -R` - give “grep” a whole directory structure, and ask it to recursively search down through it, including following all symbolic links (which `-r` does not). Particularly handy when you “just know” that an item appears “somewhere” but are not sure where.
- e.g. `grep -R -i "PermitRootLogin" /etc/*` - you know that “PermitRootLogin” is an ssh parameter in a config file somewhere under /etc, but can’t recall exactly where it is kept
- this only works on plain text files, it’s most useful for the `/etc` and `/var/log` folders
- `-i` makes the search “case insensitive”
- some older logs are compressed to save disk space and you can’t read them with `less` or search them with `grep`. `zless` and `zgrep` work on ordinary as well as compressed files.

- `which` command is useful to know where a command is being run from (where is the binary coming from). 
- `echo $PATH` - general rule is that the system will search through the locations setup in your “path”
- `which nano` - to see where `nano` comes from

```bash
$ which grep
/usr/bin/grep
$ which vi
/usr/bin/vi
$ which service
/usr/sbin/service
$ which reboot
/usr/sbin/reboot
```

### Extension

The `-exec` feature of the `find` command allows you to perform actions (to specify a command to be executed) on the files or directories found by the `find` command. 
```bash
$ find /path/to/search -exec command {} \;
# `{}` is a placeholder that gets replaced by the path of each file or directory found by `find`. The command specified by `-exec` will be executed with this placeholder replaced by the actual path.
# `\;` is a delimiter that tells `find` where the command specified by `-exec` ends. It must be escaped with a backslash (`\;`) to prevent the shell from interpreting it as a special character.

#example
$ find /home -name "*.txt" -exec ls -l {} \;
# search starts from the `/home` directory, `find` looks for files with a `.txt` extension and for each `.txt` file found, the `ls -l` command is executed.
```

- `lsof` command ("List Open Files") allows users to list information about files opened by various processes. It provides extensive details about files, sockets, pipes, devices, and other resources that are currently being accessed by active processes on the system.
- `lsof -u username` to list files opened by a specific user
- `lsof -p PID` to list files opened by a specific process
- `lsof /path/to/directory` to list files opened within a specific directory or file
- `lsof -i` to list network connections
- `lsof -i :port_number` to list processes that are listening on that port
- `lsof +L1` to identify files that are deleted but still held open by a process
- `lsof -d file_descriptor` to list files opened by a specific file descriptor
<br>

- `fuser` command ("File User") is used to identify processes that are currently using or have open files, directories, or sockets.
- `fuser /path/to/file` lists the process IDs (PIDs) of the processes that are accessing that file or directory
- `fuser -k /path/to/file` to send a signal SIGKILL (terminates those processes) to the processes accessing the specified file or directory
- `fuser -n tcp port_number` to list the processes using that port
- `fuser -v /path/to/file` provides more verbose output, including the names of the processes associated with the PIDs
- `fuser -s /path/to/file` to suppress output if no processes are found

### Task

Find all files that have the word “Permission” in it

```bash
$ find / -type f -name "*Permission*"
# search the entire system starting from the root directory `/` to find all files that contain the word "Permission" in their names
```

## Day 12 - Transferring files

There are a wide range of ways a Linux server can share files, including:
- SMB: Microsoft’s file sharing, useful on a local network of Windows machines
- AFP: Apple’s file sharing, useful on a local network of Apple machines
- WebDAV: Sharing over web (http) protocols
- FTP: Traditional Internet sharing protocol
- scp: Simple support for copying files
- rsync: Fast, very efficient file copying
- SFTP: file access and copying over the SSH protocol (despite the name, the SFTP protocol at a technical level is completely unrelated to traditional FTP)

Each of these have their place, but for copying files back and forth from your local desktop to your server, SFTP has a number of key advantages:
- No extra setup is required on your server
- Top quality security
- Allows browsing through the directory structure
- You can create and delete folders
<br>

- the same underlying protocol is being used with SFTP as with SSH
- client software is required to use SFTP
- command-line client called _sftp_ comes standard on every Apple OSX or Linux system (on Linux, there is a built-in GUI client via file manager)
. This will allow you to easily attach to remote servers via SFTP. (For the Nautilus file manager for example, press ctrl + L to bring up the ‘location window” and type: _sftp://username@myserver-address_).

Although Windows and Apple macOS have only third-party options available:
- WinSCP or FileZilla - for Windows users
- CyberDuck or FileZilla - for macOS users

### Task and instructions

- Create an `images` folder under your “home” folder on the server, and upload some images to it from your desktop machine
- Go up to the root directory. Try to create an “`images`” folder here too (fail because you are logged in as an ordinary use, so you won’t have permission to create new files or folders)
- Download a file from the server
- Synchronize a backup

```bash
~$ sftp -i ~/lupskillc.pem ubuntu@3.70.117.198
Connected to 3.70.117.198.

sftp> put Screenshot_17.png
Uploading Screenshot_17.png to /home/ubuntu/imagesScreenshot_17.png
Screenshot_17.png                                                                     100% 1636KB   1.4MB/s   00:01
sftp> ls -l Screenshot_17.png
-rwxrwxr-x    ? 1000     1000      1675573 Mar 30 23:01 Screenshot_17.png

sftp> pwd
Remote working directory: /
sftp> mkdir images
remote mkdir "/images": Permission denied

sftp> get attackers.txt
Fetching /home/ubuntu/attackers.txt to attackers.txt
attackers.txt                                                                         100%  922    13.2KB/s   00:00
sftp> lls -l attackers.txt
-rw-r--r-- 1 adinp adinp 922 Mar 30 23:06 attackers.txt

$ rsync -avz -e "ssh -i ~/lupskillc.pem" /home/adinp/images ubuntu@3.70.117.198:/home/ubuntu/images
```

## Day 13 - Users and Groups

- `sudo adduser helen` to add new user “helen”
- names are case-sensitive in Linux, so “Helen” would be a completely different user
- if it didn’t ask you for a password for your new user, then set it manually by `sudo passwd helen`
- you now have a new entry in the simple text database of users: `/etc/passwd` 
- also in a group of the same name in the file: `/etc/group`
- hash of the password for the user is in: `/etc/shadow` (you can read this too if you use “sudo”)
- these simple text files are the whole Linux user database and you could even create your users and groups by directly editing these files - although this isn’t normally recommended
- `adduser` will have created a home directory, `/home/helen` for example, with the correct permissions.
- **ATTENTION!** `useradd` is not the same as `adduser`. They both create a new user, but they interact very differently. `useradd` is a low level utility for adding users and administrators should usually use `adduser` instead. `adduser` add users to the system according to command line options and configuration information in `/etc/adduser.conf`, it is friendlier front end to the low level tool `useradd`, by default choosing Debian policy conformant UID and GID values, creating a home directory with skeletal configuration, running a custom script, and other features.

```bash
$ grep "helen" /etc/passwd
helen:x:1003:1003:,,,:/home/helen:/bin/bash
$ grep "helen" /etc/group
helen:x:1003:
$ sudo ls -l /home | grep helen
drwxr-x---  2 helen    helen    4096 Mar 31 10:51 helen
```

- `sudo groupadd developers` to create group "developers" (e.g. we want to all of the developers in my organization to have access to the same things)
- on most modern Linux systems there is a group created for each user, so user “ubuntu” is a member of the group “ubuntu”
- you can create a new user directly into an existing group, using the `ingroup` flag
- `sudo adduser --ingroup developers fred` to create a new user "fred" in group "developers"
<br>

- users can be part of more than one group
- groups can be added as required
- `groups` to see what groups you’re a member of
- on an Ubuntu system the first user created (in your case `ubuntu`), should be a member of the groups: `ubuntu`, `sudo` and `admin` 
- `sudo usermod -a -G group user` to add a user to an existing group 
- first user has the power to run `sudo` (by being a member of the group `sudo`)
- `sudo su helen` and then `groups` to switch to (become) helen and check which groups `helen` is a member of (or just `groups helen`)
- `sudo usermod -a -G sudo helen` to add "helen" to "sudo" group
<br>

- new user is just an ordinary user and so can’t use `sudo` to run commands with elevated privileges until we set them up
- we could simply add them to a group that’s pre-defined to be able to use sudo to do anything as root, but we don’t want to give quite that same amount of power

- `ls -l /etc/sudoers` to look at the permissions for the file
- `sudo less /etc/sudoers` - this is where the magic is defined
- run `visudo` to (edit) add a new entry in there for your new user (to run this, temporarily “become root” by `sudo -i`)
- all lines in `/etc/sudoers` beginning with “#” are optional comments

```bash
$ ls -l /etc/sudoers
-r--r----- 1 root root 1671 Feb  8  2022 /etc/sudoers
```

- `visudo` command will automatically check your syntax, and won’t allow you to save if there are mistakes, because a corrupt sudoers file could lock you out of your server!
- you’ll want to add some lines like this (you can add these line in wherever seems reasonable):
```bash
# Allow user "fred" to run "sudo reboot"
# ...and don't prompt for a password
#
fred ALL = NOPASSWD:/sbin/reboot` 
```

- `exit` to become your normal user again (prompt reverts to `$` from `#`)
- `sudo su helen` to login as your test user "helen" and try `sudo reboot`

## Day 14 - Who has permission?

- Files on a Linux system always have associated “permissions” - controlling who has access and what sort of access
- all files are tagged with both the name of the user and the group that owns them

```bash
$ ls -l
-rw------- 1 steve staff 4478979 6 Feb 2011 private.txt # Steve has `rw` (ie Read and Write) permission, but neither the group “staff” nor “other people” have any permission at all
-rw-rw-r-- 1 steve staff 4478979 6 Feb 2011 press.txt # Steve can Read and Write to this file too, but so can any member of the group “staff” and anyone, i.e. “other people”, can read it
-rwxr-xr-x 1 steve staff 4478979 6 Feb 2011 upload.bin # Steve has `rwx`, he can read, write and execute - i.e. run this program - but the group and others can only read and execute it
```

- these files are owned by user “steve” and the group “staff”
- anyone that is not “steve” or is not part of the group “staff” is considered “other”
- others may still have permissions to handle these files, but they do not have any ownership
- `sudo chown user file` to change the ownership of a file
- `sudo chown user:group file` to change user and group at the same time
- `sudo chgrp group file` to change only the group owner
<br>

- `-rw-r--r--` - see these as potentially three groups of “rwx”: the permission granted to the “user” who owns the file, the “group”, and “other people” (UGO)
- use `chmod` utility to change the permissions on any file

```bash
# Create a simple text file in your home directory with `vim` 
$ vim tuesday.txt
# Check that you can list its contents with `cat tuesday.txt` 
$ cat tuesday.txt
t
u
e
s
d
a
y
tuesday
# Look at its permissions with `ls -ltr tuesday.txt`
$ ls -ltr tuesday.txt
-rw-rw-r-- 1 ubuntu ubuntu 22 Mar 31 21:19 tuesday.txt
# file is owned by the user “ubuntu”, and group “ubuntu”, who are the only ones that can write to the file - but any other user can only read it
```

- `chmod u-w tuesday.txt` to remove the permission of the user to write their own file
- `chmod g-w tuesday.txt` to remove the permission of the “ubuntu” group to write their own file
- `chmod o-r tuesday.txt` to remove the permission for “others” to read the file

```bash
# task: Change file permissions
$ ls -ltr tuesday.txt
-r--r----- 1 ubuntu ubuntu 22 Mar 31 21:19 tuesday.txt

# task: Change the ownership of a file to root
$ touch one.txt
$ ls -ltr one.txt
-rw-rw-r-- 1 ubuntu ubuntu 0 Mar 31 21:58 one.txt
$ sudo chown root:root one.txt
$ ls -ltr one.txt
-rw-rw-r-- 1 root root 0 Mar 31 21:58 one.txt
```

- `chmod u+w tuesday.txt` to give yourself back the permission to write to the file

### Extension

#### ACLs

ACLs are a second level of discretionary permissions, that may override the standard ugo/rwx ones. When used correctly they can grant you a better granularity in setting access to a file or a directory, for example by giving or denying access to a specific user that is neither the file owner, nor in the group owner. ACLs are set using the setfacl command. This command allows you to add specific users and groups and define their permissions for a file or directory.

```bash
setfacl -m u:username:permissions /path/to/file # username is the user for which you're setting permissions, and permissions can be read (r), write (w), execute (x), or a combination of these
setfacl -m g:groupname:permissions /path/to/file # groupname is the group for which you're setting permissions, and permissions can be read (r), write (w), execute (x), or a combination of these
```

You can view the ACLs set on a file or directory using the `getfacl` command: `getfacl /path/to/file`. In addition to specifying ACLs for individual files or directories, Linux also supports default ACLs. Default ACLs are applied to any new files or directories created within a specific directory (using command `setfacl` with `-d` option). Not all file systems support ACLs, but most modern Linux file systems such as ext4, XFS, and others do. ACLs are particularly useful in scenarios where you need to grant specific permissions to multiple users or groups for a particular file or directory, without changing the traditional owner-group-world permission system.

#### SELinux

SELinux (Security-Enhanced Linux) is a mandatory access control (MAC) security mechanism implemented in the Linux kernel to provide an additional layer of security beyond the traditional discretionary access control (DAC) model.
- In the traditional DAC model, permissions are granted based on the user and group ownership of files and directories. Users can control access to their own files, and administrators can set broader permissions. However, this model has limitations in protecting the system from potential security threats. In contrast, MAC systems like SELinux enforce access control based on policies defined by system administrators, regardless of user ownership. This allows for more granular control over access and enhances the overall security posture of the system.
- SELinux operates based on security policies that define rules governing access to system resources such as files, directories, network ports, and processes. These policies specify what actions are allowed or denied for different types of subjects (users, processes) on different types of objects (files, directories, etc.).
- SELinux assigns security labels, known as security contexts, to various system objects such as files, directories, processes, and network ports. These labels include information about the type and level of access allowed for the object.
- SELinux operates in one of three modes: enforcing, permissive, or disabled.
    - Enforcing mode - SELinux actively enforces security policies by denying or allowing access based on defined rules. Violations are logged.
    - Permissive mode - SELinux does not enforce policies but still logs violations. This mode is useful for troubleshooting and policy development.
    - Disabled mode - SELinux is completely disabled, and DAC controls are used exclusively.
- SELinux incorporates RBAC (Role-Based Access Control) principles, where roles define sets of permissions and users are assigned to roles. This allows for more flexible access control configurations.
- SELinux provides extensive auditing capabilities, allowing administrators to monitor and analyze security-related events on the system.

#### AppArmor

AppArmor (Application Armor) is a Linux security module that provides Mandatory Access Control (MAC) to restrict the capabilities of individual programs. Like SELinux, it aims to enhance system security by confining programs to a limited set of resources and actions they are allowed to perform.
- AppArmor uses security profiles to define the allowed actions and access rights for specific programs. These profiles specify which files, directories, network sockets, and other resources a program can access, as well as the operations it can perform on them.
- AppArmor primarily uses path-based enforcement, where access permissions are defined based on file paths rather than labels as in SELinux. This approach simplifies the configuration and management of security policies.
- With AppArmor, administrators can specify fine-grained access control rules for each program. For example, they can restrict a web server to only access its configuration files and the necessary network ports, or confine a web browser to a sandboxed environment to prevent it from accessing sensitive system resources.
- AppArmor profiles can be in one of two modes: enforcement or complain.
    - Enforcement Mode - AppArmor actively enforces the security policies defined in the profiles. If a program attempts to perform an action that violates the policy, the action is blocked, and an audit message is logged.
    - Complain Mode - AppArmor logs violations of the security policies but does not enforce them. This mode is useful for testing and debugging profiles before enforcing them.
- AppArmor is integrated into several Linux distributions, including Ubuntu, Debian, and openSUSE. It is often enabled by default and can be managed using utilities such as apparmor_parser and aa-status.
- Compared to SELinux, AppArmor is often considered easier to set up and manage, especially for users who are not security experts. Its path-based approach and simpler configuration syntax make it more approachable for administrators.
- AppArmor provides auditing features to track and analyze security-related events, allowing administrators to monitor the behavior of confined programs and identify potential security issues.

## Day 15 - Deeper into repositories…

Any particular Linux installation has a number of important characteristics:
- Version - e.g. Ubuntu 20.04, CentOS 5, RHEL 6
- “Bit size” - 32-bit or 64-bit
- Chip - Intel, AMD, PowerPC, ARM

The version number is particularly important because it controls the versions of application that you can install. When Ubuntu 18.04 (April 2018) was released, it came out with Apache 2.4.29. So, if your server runs 18.04, then even if you installed Apache with `apt` five years later that is still the version you would receive. This provides stability, but at an obvious cost for web designers who hanker after some feature which later versions provide. (Security patches are made to the repositories, but by “backporting” security fixes from later versions into the old stable version that was first shipped).

- As package manager, Debian and Ubuntu distributions use the `apt` command, but for most purposes the competing `yum` and `dnf` commands used by Fedora, RHEL, CentOS and Scientific Linux work in a very similar way
- The configuration is done with files under the `/etc/apt` directory
- `less /etc/apt/sources.list` to see where the packages you install are coming from (you’ll see lines that are clearly specifying URLs to a “repository” for your specific version: `deb http://archive.ubuntu.com/ubuntu precise-security main restricted universe`)

While there’s an amazing amount of software available in the “standard” repositories (more than 3,000 for CentOS and ten times that number for Ubuntu), there are often packages not available - typically for one of two reasons:
- Stability - CentOS is based on RHEL (Red Hat Enterprise Linux), which is firmly focussed on stability in large commercial server installations, so games and many minor packages are not included
- Ideology - Ubuntu and Debian have a strong “software freedom” ethic (this refers to freedom, not price), which means that certain packages you may need are unavailable by default
<br>

- `apt-cache dump` to get the full list and details how many packages you could already install (Ctrl+C to stop that)

```bash
# filter out just the packages names and count them 
$ apt-cache dump | grep "Package:" | wc -l 
115849
```

- often the “Universe” and “Multiverse” repositories are disabled by default
- Multiverse contains software which has been classified as non-free …may not include security updates
- Examples of useful tools in Multiverse might include the compression utilities `rar` and `lha`, and the network performance tool `netperf`

To enable the “Multiverse” repository:

```bash
$ sudo vi /etc/apt/sources.list

# uncomment these lines

deb http://eu-central-1.ec2.archive.ubuntu.com/ubuntu/ jammy multiverse
deb-src http://eu-central-1.ec2.archive.ubuntu.com/ubuntu/ jammy multiverse
deb http://eu-central-1.ec2.archive.ubuntu.com/ubuntu/ jammy-updates multiverse
deb-src http://eu-central-1.ec2.archive.ubuntu.com/ubuntu/ jammy-updates multiverse

# after adding this, update your local cache of available applications
$ sudo apt update

# install `netperf`
$ sudo apt install netperf
...
Get:1 http://eu-central-1.ec2.archive.ubuntu.com/ubuntu jammy/multiverse amd64 netperf amd64 2.7.0-0.1 [540 kB]
...
# output shows that it’s coming from Multiverse
```

Ubuntu also allows users to register an account and setup software in a Personal Package Archive (PPA) - typically these are setup by enthusiastic developers, and allow you to install the latest “cutting edge” software. As an example, install and run the `neofetch` utility. When run, this prints out a summary of your configuration and hardware. This is in the standard repositories, and `neofetch --version` will show the version. If for some reason you wanted to be have a later version you could install a developer’s Neofetch PPA to your software sources by:

```bash
$ sudo add-apt-repository ppa:ubuntusway-dev/dev

$ sudo apt update # after adding a repository, update your local cache of available applications

$ sudo apt install neofetch # install the package

$ neofetch --version # check version
Neofetch 7.1.0

$ apt-cache show neofetch # see the details of the package
Package: neofetch
Priority: optional
Section: utils
Installed-Size: 388
Maintainer: Nobuhiro Iwamatsu <iwamatsu@debian.org>
...
```

Installing only from the default repositories is clearly the safest, but there are often good reasons for going beyond them. As a sysadmin you need to judge the risks, but in the example we came up with a realistic scenario where connecting to an unstable working developer’s version made sense.

As general rule however you:
- Will seldom have good reasons for hooking into more than one or two extra repositories
- Need to read up about a repository first, to understand any potential disadvantages.
