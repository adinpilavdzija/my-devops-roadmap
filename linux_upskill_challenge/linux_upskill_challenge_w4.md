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
