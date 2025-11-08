# Linux Upskill Challenge - Week 4

* [Day 16](docs/16.md) - **Archiving and compressing**: Understanding and using `tar` and `gzip`
* [Day 17](docs/17.md) - **Build from the source**: Installing from source. Discussion, using `wget` to get a tarball, `tar` to extract and then configure, make and install. Discussion of security, maintenance issues.
* [Day 18](docs/18.md) - **Log rotation**: Log management and rotation, `logrotate`
* [Day 19](docs/19.md) - **Inodes, symlinks and other shortcuts**: Inodes, hard links symlinks and `stat`
* [Day 20](docs/20.md) - **Scripting**: Understanding how scripting work in Linux, the shebang, permissions and $PATH. A couple of simple sample scripts based on the filtering of logs we've been doing. Resources to explore further.
* [Day 21](docs/21.md) - **What's next?**: Closing the course with some suggestions.

## Day 16 - Archiving and compressing

- On other operating systems, applications like WinZip, and pkzip before it, have long been used to gather a series of files and folders into one compressed file with a .zip extension
- Linux takes a slightly different approach, with the “gathering” of files and folders done in one step, and the compression in another.
- `tar -cvf myinits.tar /etc/init.d/` to create a “snapshot” of the current files in your /etc/init.d folder which creates myinits.tar
- “tar” is short for “tape archive”
- `-f` switch specifies that “the output should go to the filename which follows” (the order of the switches is important)
- `tar` considers anything after `-f` as the name of the archive that needs to be created, so `-f` should be the last flag while creating an archive
- `-v` switch (verbose) is included to give some feedback (traditionally many utilities provide no feedback unless they fail)
- `gzip myinits.tar` to compress this file with GnuZip, which will create `myinits.tar.gz`
- compressed tar archive like this is known as a “tarball”
- tarballs sometimes have a ".tgz" extension 
- `tar -cvzf myinits.tgz /etc/init.d/` to do the two steps in one with the “-z” switch (to compress the result)
- `-c` switch say that we’re creating an archive
- `-v` to make the command “verbose”
- `-f` to specify the output file

### Task

- Create a tarball
- Create a compressed tarball and compare sizes
- Extract files from a tarball

```bash
$ tar -cvf archive.tar Screenshot_17.png cloudformation.yaml tuesday.txt # Create a tarball

$ tar -czvf archive.tar.gz Screenshot_17.png cloudformation.yaml tuesday.txt # Create a compressed tarball

$ ls -lh archive.tar archive.tar.gz # compare sizes
-rw-rw-r-- 1 ubuntu ubuntu 1.7M Apr  1 17:37 archive.tar
-rw-rw-r-- 1 ubuntu ubuntu 1.6M Apr  1 17:37 archive.tar.gz

$ tar -xvf archive.tar # Extract files from a tarball
Screenshot_17.png
cloudformation.yaml
tuesday.txt
```

### Extension

What is a .bz2 file - and how would you extract the files from it?
- A .bz2 file is a compressed file format similar to .zip or .gzip, but it's compressed using the BZip2 algorithm. It's commonly used for compressing large files or archives. You can use the bzip2 command line tool. To extract, you'd use the -d flag to decompress: `bzip2 -d file.bz2`. This command will extract the contents of file.bz2 and create a new file without the .bz2 extension.

Research how absolute and relative paths are handled in tar - and why you need to be careful extracting from archives when logged in as root
- Absolute Paths: When you create a tar archive with absolute paths, the entire path from the root directory is preserved within the archive. For example, if you create a tar archive `/home/user/archive.tar` containing files from `/home/user/data/file1.txt` and `/home/user/data/file2.txt`, the paths stored within the archive will be absolute (`/home/user/data/file1.txt`, `/home/user/data/file2.txt`). When you extract this archive, the files will be extracted to the exact same absolute locations on your file system.
- Relative Paths: In contrast, when you create a tar archive with relative paths, only the paths relative to the current directory are stored within the archive. For example, if you create a tar archive `archive.tar` containing files from `/home/user/data/file1.txt` and `/home/user/data/file2.txt`, the paths stored within the archive will be relative (`data/file1.txt`, `data/file2.txt`). When you extract this archive, the files will be extracted relative to the current directory.
- When you extract archives as the root user, you have full privileges to overwrite any files on the system, regardless of their permissions. It's important to double-check the contents of the archive before extracting it (use the -t option with tar to list the contents of the archive). You can consider extracting archives as a regular user whenever possible to limit the scope of potential damage. Extracting archives as the root user can be dangerous if the archive contains files with absolute paths or if you're not careful with relative paths. For example: 
    - If the archive contains files with absolute paths and you extract it as root, it can overwrite critical system files, leading to system instability or compromise.
    - If the archive contains files with relative paths and you extract it as root, it can overwrite files in directories where you might not intend to make changes, leading to unintended consequences.

You might notice that some tutorials write “tar cvf” rather than “tar -cvf” with the switch character - do you know why?
- use of the switch character (`-`) before options is a convention to clearly indicate that what follows are options rather than arguments
- historically some versions of `tar` did not require the switch character before options and it still remains optional in many versions
- both `tar cvf` and `tar -cvf` are valid and functionally equivalent

## Day 17 - Build from the source

We’re going to “go to the source”. The applications we’ve been installing up to this point have come from repositories. The files there are “binaries” - pre-compiled, and often customised by your distro. What might not be clear is that your distro gets these applications from a diverse range of un-coordinated development projects (the “upstream”), and these developers are continuously working on new versions. We’ll go to one of these, download the source, compile and install it. (Another big part of what package managers like `apt` do, is to identify and install any required “dependencies”. In the Linux world many open source apps take advantage of existing infrastructure in this way, but it can be a very tricky thing to resolve manually. However, the app we’re installing today from source is relatively unusual in being completly standalone).

Projects normally provide their applications as “source files”, written in the C, C++ or other computer languages. We’re going to pull down such a source file, but it won’t be any use to us until we compile it into an “executable” - a program that our server can execute. So, we’ll need to first install a standard bundle of common compilers and similar tools. On Ubuntu, the package of such tools is called “build-essential”. Install it with `sudo apt install build-essential`.

```bash
$ nmap -v # Check the nmap version
Starting Nmap 7.94 ( https://nmap.org ) at 2024-04-02 13:10 CEST
Read data files from: /snap/nmap/3262/usr/bin/../share/nmap
WARNING: No targets were specified, so 0 hosts scanned.
Nmap done: 0 IP addresses (0 hosts up) scanned in 0.05 seconds

$ which nmap # see where the executable is stored
/snap/bin/nmap

$ wget -v https://nmap.org/dist/nmap-7.93.tar.bz2 # download the file in home directory from http://nmap.org/ - “Source Code Distribution” - “Latest development nmap release tarball”

$ ls -ltr nmap-7.93.tar.bz2
-rw-rw-r-- 1 ubuntu ubuntu 10823114 Sep  2  2022 nmap-7.93.tar.bz2

$ tar -jxvf nmap-7.93.tar.bz2 # uncompress a bz2 file and extract

$ ls -l|grep nmap
drwxr-xr-x 24 ubuntu ubuntu     4096 Sep  2  2022 nmap-7.93
-rw-rw-r--  1 ubuntu ubuntu 10823114 Sep  2  2022 nmap-7.93.tar.bz2

$ ls -l nmap-7.93
total 14592
-rw-r--r--  1 ubuntu ubuntu     173 Jun 30  2009 BSDmakefile
-rw-r--r--  1 ubuntu ubuntu  767893 Sep  1  2022 CHANGELOG
-rw-r--r--  1 ubuntu ubuntu    2662 Aug 23  2017 CONTRIBUTING.md
-rw-r--r--  1 ubuntu ubuntu  105728 Feb 18  2022 FPEngine.cc
-rw-r--r--  1 ubuntu ubuntu   17600 Feb 18  2022 FPEngine.h
-rw-r--r--  1 ubuntu ubuntu 1429321 Oct  6  2020 FPModel.cc
...

$ cat nmap-7.93/README.md
...
Installing
----------
Ideally, you should be able to just type:

    ./configure
    make
    make install

For far more in-depth compilation, installation, and removal notes, read the
[Nmap Install Guide](https://nmap.org/book/install.html) on Nmap.org.
...

$ ./configure

$ make

$ sudo make install

$ sudo updatedb # these files have only just been added and we need to manually update the index of files

$ locate bin/nmap # allows very fast searching for files
/snap/bin/nmap
/usr/local/bin/nmap
...
$ /snap/bin/nmap -V
Nmap version 7.94 ( https://nmap.org )
...
$ /usr/local/bin/nmap -V
Nmap version 7.93 ( https://nmap.org )
...
```

- By convention, source files will typically include in their root directory a series of text files in uppercase such as: README and INSTALLATION. 
- It’s important to realise that the programmers of the “upstream” project are not writing for Ubuntu, CentOS - or even Linux. They have written a correct working program in C or C++ etc and made it available, but it’s up to us to figure out how to compile it for our operating system, chip type etc. 
- This hopefully gives a little insight into the value that distributions such as CentOS, Ubuntu and utilities such as `apt`, `yum` etc add, and how tough it would be to create your own Linux From Scratch
<br>

- `./configure` - is a script which checks your server (ie to see whether it’s ARM or Intel based, 32 or 64-bit, which compiler you have etc). It can also be given parameters to tailor the compilation of the software, such as to not include any extra support for running in a GUI environment - something that would make sense on a “headless” (remote text-only server), or to optimize for minimum memory use at the expense of speed - as might make sense if your server has very little RAM. If asked any questions, just take the defaults - and don’t panic if you get some WARNING messages, chances are that all will be well.
- `make` - compiles the software, typically calling the GNU compiler `gcc`. This may generate lots of scary looking text, and take a minute or two - or as much as an hour or two for very large packages like LibreOffice.
- `make install` - this step takes the compiled files, and installs that plus documentation to your system and in some cases will setup services and scheduled tasks etc. Until now you’ve just been working in your home directory, but this step installs to the system for all users, so requires `root` privileges. Because of this, you’ll need to actually run: `sudo make install`. If asked any questions, just take the defaults.

- potentially this last step will have overwritten the `nmap` you already had, but more likely this new one has been installed into a different place.

- `/bin` is for key parts of the operating system
- `/usr/bin` for less critical utilities 
- `/usr/local/bin` for software you’ve chosed to manually install yourself
- When you type a command it will search through each of the directories given in your PATH environment variable, and start the first match. So, if `/bin/nmap` exists, it will run instead of `/usr/local/bin` - but if you give the “full path” to the version you want - such as `/usr/local/bin/nmap` - it will run that version instead.

NOTE: Because you’ve done all this outside of the `apt` system, this binary won’t get updates when you run `apt update`. Not a big issue with a utility like `nmap` probably, but for anything that runs as an exposed service it’s important that you understand that you now have to track security alerts for the application (and all of its dependencies), and install the later fixed versions when they’re available. This is a significant pain/risk for a production server.

## Day 18 - Logs, monitoring and troubleshooting

- logs are your best friend, but disk space problems can be your worst enemy
- `logrotate` application keeps your logs in check (you can define how many days of logs you wish to keep; split them into manageable files; compress them to save space, or even keep them on a totally separate server)

```bash
# logs are already being rotated
/var/log/apache2$ ls
access.log        access.log.14.gz  access.log.7.gz  error.log.11.gz  error.log.4.gz  other_vhosts_access.log
access.log.1      access.log.2.gz   access.log.8.gz  error.log.12.gz  error.log.5.gz
access.log.10.gz  access.log.3.gz   access.log.9.gz  error.log.13.gz  error.log.6.gz
access.log.11.gz  access.log.4.gz   error.log        error.log.14.gz  error.log.7.gz
access.log.12.gz  access.log.5.gz   error.log.1      error.log.2.gz   error.log.8.gz
access.log.13.gz  access.log.6.gz   error.log.10.gz  error.log.3.gz   error.log.9.gz
```

- `cron` is generally setup to run scripts in `/etc/cron.daily`, there you should see a script called `logrotate` (or possibly `00logrotate` to force it to be the first task to run)
- overall configuration is set in `/etc/logrotate.conf`, also look at the files under the directory `/etc/logrotate.d` (contents of these are merged in to create the full configuration)

```bash
$ cat /etc/logrotate.d/apache2
/var/log/apache2/*.log {
    daily
    missingok
    rotate 14
    compress
    delaycompress
    notifempty
    create 640 root adm
```

- any apache2 .log file will be rotated each week, with 52 compressed copies being kept.
- typically when you install an application a suitable logrotate “recipe” is installed for you, so you’ll not normally be creating these from scratch
- default settings won’t always match your requirements, so it’s perfectly reasonable for you as the sysadmin to edit these 

## Day 19 - Inodes, symlinks and other shortcuts

- Linux supports a large number of different “filesystems” - although on a server you’ll typically be dealing with just "ext3" or "ext4" and perhaps "btrfs"
- Layer of Linux that sits above all of these is the Linux Virtual Filesystem
- VFS is a key part of Linux, and an overview of it and some of the surrounding concepts is very useful in confidently administering a system
<br>

- Linux has an extra layer between the filename and the file’s actual data on the disk - `inode`
- `inode` has a numerical value which you can see most easily with `-i` switch on the `ls` command or with the `stat` command:

```bash
$ ls -li /etc/hosts
35356766 -rw------- 1 root root 260 Nov 25 04:59 /etc/hosts

$ stat /etc/hosts
  File: /etc/hosts
  Size: 221             Blocks: 8          IO Block: 4096   regular file
Device: ca01h/51713d    Inode: 45          Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2024-04-04 21:08:45.316000000 +0200
Modify: 2023-12-06 23:36:20.000000000 +0100
Change: 2023-12-07 03:13:40.967968954 +0100
 Birth: 2023-12-07 03:13:40.967968954 +0100
```

- Every file name “points” to an inode, which in turn points to the actual data on the disk
- several filenames could point to the same inode and hence have exactly the same contents (“hard link”)
- the permissions, ownership and dates of filenames are actually kept at the inode level, not the filename

```bash
$ cd
# use the `ln` (“link”) command to create a “hard link”
$ sudo ln /etc/passwd link1
# use the `ln -s` command to create a “symbolic link” or “symlink”
$ ln -s /etc/passwd link2
# use `ls -li` to view the resulting files, and `less` or `cat` to view them
$ ls -li | grep link
  6085 -rw-r--r--  2 root   root       2055 Mar 31 10:51 link1
258596 lrwxrwxrwx  1 ubuntu ubuntu       11 Apr  4 21:14 link2 -> /etc/passwd
```

- permissions on a symlink generally show as allowing everthing, but what matters is the permission of the file it points to
- symlinks are especially common for e.g. `ls -ltr /etc/rc2.d/*`. This directory holds all the scripts that start when your machine changes to “runlevel 2” (its normal running state), but you’ll see that in fact most of them are symlinks to the real scripts in "/etc/init.d"
- It’s also very common to have something like this below (where the program “prog”, is a symlink - originally to v3, but now points to v4 (and could be pointed back if required)):
```bash
 prog
 prog-v3
 prog-v4` 
```

Hard links:
- Only link to a file, not a directory
- Can’t reference a file on a different disk/volume
- Links will reference a file even if it is moved
- Links reference inode/physical locations on the disk

Symbolic (soft) links:
- Can link to directories
- Can reference a file/folder on a different hard disk/volume
- Links remain if the original file is deleted
- Links will NOT reference the file anymore if it is moved
- Links reference abstract filenames/directories and NOT physical locations.
- They have their own inode

### Task

```bash
$ sudo ln /etc/passwd link1 # Create a hard link

$ ln -s /etc/passwd link2 # Create a soft link

# Create aliases
$ alias csadin='cowsay adin'
ubuntu@linuxupskillchallenge:~$ csadin
 ______
< adin >
 ------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||

$ alias cdl='cd /var/log'
$ pwd
/home/ubuntu
$ cdl
$ pwd
/var/log
```

## Day 20 - Scripting

When typing at the Linux command-line you’re directly communicating with “the command interpreter”, also known as “the shell”. Normally this shell is "bash", so when you string commands together to make a script the result can be called either a ‘“shell script”, or a “bash script”.

Why make a script rather than just typing commands in manually?
- It saves typing. Remember when we searched through the logs with a long string of `grep`, `cut` and `sort` commands? If you need to do something like that more than a few times then turning it into a script saves typing and typos!
- Parameters. One script can be used to do several things depending on what parameters you provide
- Automation. Pop your script in `/etc/cron.daily` and it will run each day, or install a symlink to it in the appropriate `/etc/rc.d` folder and you can have it run each time the system is shut down or booted up.
<br>

- Scripts are just simple text files, but if you set the “execute” permissions on them then the system will look for a special line starting with the two characters “#” and “!” referred to as the “shebang” (or “crunchbang”) at the top of the file. This line typically looks like this: `#!/bin/bash` .
- Normally anything starting with a “#” character would be treated as a comment, but in the first line and followed by a “!”, it’s interpreted as: “please feed the rest of this to the /bin/bash program, which will interpret it as a script”. 
- our scripts will be written in the bash language, but scripts can also be written in many other “scripting languages”, so a script in the Perl language might start with `#!/usr/bin/perl` and one in Python `#!/usr/bin/env python3`
<br>

```bash
# write a small script to list out who’s been most recently unsuccessfully trying to login to your server, using the entries in /var/log/auth.log

$ cat attacker
#!/bin/bash
#
#   attacker - prints out the last failed login attempt
#
echo "The last failed login attempt came from IP address:"
grep -i "disconnected from" /var/log/auth.log|tail -1| cut -d: -f4| cut -f7 -d" "
```

- Putting comments at the top of the script like this isn’t strictly necessary (the computer ignores them), but it’s a good professional habit to get into
- `chmod +x attacker` to make it executable 
- to run this script, you just need to refer to it by name. Current directory is (deliberately) not in your $PATH, so you need to do this either with `/home/support/attacker` or `./attacker` 
- Once you’re happy with a script, and want to have it easily available, move it somewhere on your $PATH. `/usr/local/bin` is a normally the appropriate place: `sudo mv attacker /usr/local/bin/attacker`. Now it will Just Work whenever you type `attacker`
<br>

- You can expand this script so that it requires a parameter and prints out some syntax help when you don’t give one

```bash
#
##   topattack - list the most persistent attackers
#
if [ -z "$1" ]; then
echo -e "\nUsage: `basename $0` <num> - Lists the top <num> attackers by IP"
exit 0
fi
echo " "
echo "Persistant recent attackers"
echo " "
echo "Attempts      IP "
echo "-----------------------"
grep "Disconnected from authenticating user root" /var/log/auth.log|cut -d: -f 4 | cut -d" " -f7|sort |uniq -c |sort -nr |head -$1
```

- you can use `whois` to find details on any of these IPs

### Task

```bash
# Write a short script that list the top 3 IP addresses that tried to login into your server

#!/bin/bash
#
#   top_attackers - prints out the top 3 IP addresses with the most failed login attempts
#

echo "Top 3 IP addresses with the most failed login attempts:"
grep -i "disconnected from" /var/log/auth.log | awk '{print $11}' | cut -d= -f2 | sort | uniq -c | sort -nr | head -n 3
```

## Day 21 - What next?

- [Server World](https://www.server-world.info/en/note?os=Ubuntu_22.04&p=httpd&f=1) for some inspiration.
- check out any articles like “20 Linux commands every sysadmin should know”
- [Linux Journey](https://linuxjourney.com/) 
- [Linux 101 Hacks](https://linux.101hacks.com/toc/)
- [SadServers.com](https://sadservers.com/)
- [Command Line Challenge](https://cmdchallenge.com/)
- [practicelinux.com](https://www.practicelinux.com/home)
- [learnshell.org](https://www.learnshell.org/)
- [commandlinefu.com](https://www.commandlinefu.com/commands/browse)
- If your next level goal is to get into DevOps, take a look at the [DevOps Roadmap](https://roadmap.sh/devops).

Certifications:
- [CompTIA Linux+](https://www.comptia.org/certifications/linux)
- [LPI LPIC-1: Linux Administrator](https://wiki.lpi.org/wiki/Main_Page)
- [Red Hat](https://www.redhat.com/en/services/all-certifications-exams)

Affordable professional training:
- [LinkedIn Learning](https://www.linkedin.com/learning/search?keywords=linux)
- [Udemy](https://www.udemy.com/topic/linux/)
- [CBT Nuggets](https://www.cbtnuggets.com/it-training/linux-found-cert-sys-admin)
