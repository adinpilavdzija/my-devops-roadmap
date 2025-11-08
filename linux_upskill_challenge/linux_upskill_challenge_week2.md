# Linux Upskill Challenge - Week 2

* [Day 6](docs/06.md) - **Editing with "vim"**: Learning `vim`, the minimal knowledge, but also via `vimtutor`
* [Day 7](docs/07.md) - **The server and its services**: Installing Apache2, stopping and starting, altering the content, reading logs
* [Day 8](docs/08.md) - **The infamous "grep" and other text processors**: Hands-on with text tools like `grep`, `cat`, `more`, `less`, `cut`, `awk` and `tail` - and piping of course. (and a wave to `awk` and `sed`)
* [Day 9](docs/09.md) - **Diving into networking**: Looking at open ports with `ss`, and a nod to `netstat`, install `nmap` and test. Install `ufw`, set up, enable and test etc. Discuss security resonsibilities as the sysadmin.
* [Day 10](docs/10.md) - **Getting the computer to do your work for you**: Covering `cron`, `at`, and systemd timers

## Day 6 - Editing with “vim”

The Real Sysadmin uses `vi` - this is the editor that’s always installed by default - and today you’ll get started using it. Vim is actually a contraction of [Vi IMproved](https://vimhelp.org/intro.txt.html) and is a direct descendant of Vi. Very often when you type `vi`, what the system actually starts is `vim` because `vi` command is actually symlinked to `vim`:
```bash
$ vi --version
VIM - Vi IMproved 8.2 (2019 Dec 12, compiled Dec 05 2023 17:58:57)
```

- There are two “modes” with very different behaviours - “normal mode” and “insert mode”
- “normal mode” is used to input commands (`Press Esc Esc`)
- “insert mode” is used for writing text, similar to a regular text editor’s default behaviour (`Press i or insert`)
<br>

- type `:q!` in normal mode and press Enter - this quits without saving any changes 
- use the keys _h_ _j_ _k_ and _l_ to move around (this is the traditional `vi` method) then try using the arrow keys
- press _3_ then _3_ again, then _d_ and _d_ again to delete 33 lines of the file (in normal mode _33dd_ is a command that says “delete 33 lines”)
- press _u_ in normal mode to undo the last change you made
<br>

- From normal mode, type `G` to get to the bottom of the file
- try `gg` to get to the top
- search for references: type `/sun` to find the first instance of “sun”, hit enter, then press _n_ repeatedly to step through all the next occurrences
- position the cursor on a line, then (for example), type _11dd_ to delete 11 lines, then immediately paste them back in by pressing _P_
- Writing your changes to disk: From normal mode type `:w` to “write” but stay in `vim`, or `:wq` to “write and quit”
<br>

- run `vimtutor` for the “official” Vim tutorial
- In many situations as a professional, you’ll be working on other people’s systems, and they’re often very paranoid about stability. You may not have the authority to just “sudo apt install ” - even if technically you could. However, `vi` is always installed on any Unix or Linux box from tiny IoT devices to supercomputer clusters. It is actually required by the Single Unix Specification and POSIX.
- POSIX (Portable Operating System Interface) is a set of standards that define the application programming interface (API), command-line interface (CLI), and utilities interface for Unix-like operating systems. POSIX standards aim to ensure compatibility between different Unix and Unix-like systems, allowing software written for one POSIX-compliant system to be easily ported to another.
- we can make some customisation via `~/.vimrc` file

## Day 7 - The server and its services

### Task

Install and run apache, transforming your server into a _web server_

- Refresh your list of available packages (apps) by: `sudo apt update`
- Install Apache from the repository with a simple: `sudo apt install apache2`
- Apache is installed as a “service” - a program that starts automatically when the server starts and keeps running whether anyone is logged in or not
- Don’t forget to add port 80 to your instance security group to allow inbound traffic to your server. We need to be very aware that we’ve now increased the “attack surface” of your server. In addition to _ssh_ on port 22, we are now also exposing the _apache2_ code on port 80. Over time the logs may reveal access from a wide range of visiting search engines, and attackers - and that’s perfectly normal. If we regularly run the commands: `sudo apt update`, then `sudo apt upgrade`, and accept the suggested upgrades, then we’ll have all the latest security updates, and be secure enough for a test environment.
- `sudo systemctl stop apache2`- to stop apache
- `sudo systemctl start apache2` - to start apache
- `systemctl status apache2` - to check the status
- `sudo systemctl restart apache2` - stops the service and then starts it again (brief downtime)
- `sudo systemctl reload apache2` - reload service's configuration files without stopping and starting the service (without downtime)
- As with the vast majority of Linux software, configuration is controlled by files under the _/etc_ directory
- check the configuration files under `/etc/apache2`, especially `/etc/apache2/apache2.conf`
- In `/etc/apache2/apache2.conf` there’s the line with the text: “IncludeOptional conf-enabled/\*.conf”. This tells Apache that the \*.conf files in the subdirectory _conf-enabled_ should be merged in with those from `/etc/apache2/apache2.conf` at load. This approach of lots of small specific config files is common.
- The location of the default webpage is defined by the _DocumentRoot_ parameter in the file `/etc/apache2/sites-enabled/000-default.conf`.
- view the code of the default page, usually, at `/var/www/html/index.html`
- As with most Linux services, Apache keeps its logs under the `/var/log` directory
- look at the logs in `/var/log/apache2/access.log` file where you should be able to see your session from when you browsed to the test page. There is also the `error.log` file

```bash
$ sudo apt update
Hit:1 http://eu-central-1.ec2.archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://eu-central-1.ec2.archive.ubuntu.com/ubuntu jammy-updates InRelease [119 kB]
Hit:3 http://eu-central-1.ec2.archive.ubuntu.com/ubuntu jammy-backports InRelease
Get:4 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:5 http://eu-central-1.ec2.archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [1421 kB]
Fetched 1650 kB in 1s (1677 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done

$ sudo apt install apache2
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
...

$ systemctl status apache2
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2024-03-03 10:29:21 CET; 15min ago
...
```

### Extension

- systemd is an init system and system manager that has widely become the new standard for Linux distributions (note that it isn’t implemented universally across all distros)
- systemctl command is the central management tool for controlling the init system.
- The fundamental purpose of an init system is to initialize the components that must be started after the Linux kernel is booted
- The init system is also used to manage services and daemons for the server at any point while the system is running.
- For service management tasks, the target unit will be service units, which have unit files with a suffix of `.service`. However, for most service management commands, you can actually leave off the `.service` suffix, as `systemd` is smart enough to know that you probably want to operate on a service when using service management commands.
- If you are unsure whether the service has the functionality to reload its configuration, you can issue the `reload-or-restart` command. This will reload the configuration in-place if available. Otherwise, it will restart the service so the new configuration is picked up: `sudo systemctl reload-or-restart application.service`
- To tell systemd to start services automatically at boot, you must enable them: `sudo systemctl enable application.service`
- Other capabilities, like log management and user sessions are handled by separate daemons and management utilities (`journald`/`journalctl` and `logind`/`loginctl` respectively).

## Day 8 - The infamous “grep” and other text processors

### YOUR TASKS TODAY

- Dump out the complete contents of a file with `cat` like this: `cat /var/log/apache2/access.log`
- Use `less` to open the same file: `less /var/log/apache2/access.log` (move up and down through the file with your arrow keys, then use “q” to quit). Use _gg_ to go to the top of the file, _GG_ to go to the bottom of the file, _/_ to search for something, _n_ and _N_ to hop to the next “hit” or back to the previous one 
- Look at just the tail end of the file with `tail /var/log/apache2/access.log` or use `head` command to see the first lines
- Follow a log in real-time with: `tail -f /var/log/apache2/access.log` 
- Take the output of one command and “pipe” it in as the input to another by using the `|` (pipe) symbol
- Dump out a file with `cat`, but pipe that output to `grep` with a search term: `cat /var/log/auth.log | grep "authenticating"` or simplify to: `grep "authenticating" /var/log/auth.log`
- Piping allows you to narrow your search, e.g. `grep "authenticating" /var/log/auth.log | grep "root"`
- Use the `cut` command to select out most interesting portions of each line by specifying “-d” (delimiter) and “-f” (field): `grep "authenticating" /var/log/auth.log| grep "root"| cut -f 10- -d" "` (field 10 onwards, where the delimiter between field is the ” ” character)
- Use the `-v` option to invert the selection and find attempts to login with other users: `grep "authenticating" /var/log/auth.log| grep -v "root"| cut -f 10- -d" "`
- The output of any command can be “redirected” to a file with the “>” operator. The command: `ls -ltr > listing.txt` wouldn’t list the directory contents to your screen, but instead redirect into the file “listing.txt” (creating that file if it didn’t exist, or overwriting the contents if it did).

```
Re-run the command to list all the IP’s that have unsuccessfully tried to login to your server as _root_ - but this time, use the the “>” operator to redirect it to the file: `~/attackers.txt`. You might like to share and compare with others doing the course how heavily you’re “under attack”!
```

```bash
$ grep -E "error.*root|root.*error" /var/log/auth.log > attackers.txt
ubuntu@linuxupskillchallenge:~$ cat attackers.txt
Mar 12 21:14:31 linuxupskillchallenge sshd[25118]: error: maximum authentication attempts exceeded for root from 58.177.19.73 port 36829 ssh2 [preauth]
Mar 12 21:14:38 linuxupskillchallenge sshd[25120]: error: maximum authentication attempts exceeded for root from 58.177.19.73 port 37006 ssh2 [preauth]
Mar 14 06:24:25 linuxupskillchallenge sshd[26843]: error: maximum authentication attempts exceeded for root from 124.244.16.188 port 37515 ssh2 [preauth]
Mar 14 06:24:28 linuxupskillchallenge sshd[26845]: error: maximum authentication attempts exceeded for root from 124.244.16.188 port 37663 ssh2 [preauth]
Mar 14 16:39:11 linuxupskillchallenge sshd[27777]: error: maximum authentication attempts exceeded for root from 158.174.168.185 port 48771 ssh2 [preauth]
Mar 14 16:39:12 linuxupskillchallenge sshd[27779]: error: maximum authentication attempts exceeded for root from 158.174.168.185 port 48832 ssh2 [preauth]
```

### Extension

See if you can extend your filtering of `auth.log` to select just the IP addresses, then pipe this to `sort`, and then further to `uniq` to get a list of all those IP addresses that have been “auditing” your server security for you.

```bash
$ grep -E "error.*root|root.*error" /var/log/auth.log | grep -E -o '([0-9]{1,3}\.){3}[0-9]{1,3}' | sort | uniq
124.244.16.188
158.174.168.185
58.177.19.73
```

Investigate the `awk` and `sed` commands. When you’re having difficulty figuring out how to do something with `grep` and `cut`, then you may need to step up to using these. Googling for “linux sed tricks” or “awk one liners” will get you many examples. Aim to learn at least one simple useful trick with both `awk` and `sed`

```bash
# sed commands
$ sed 's/old_text/new_text/g' filename #Substitute 'old_text' with 'new_text' in a file
$ sed '/pattern_to_delete/d' filename #Delete lines matching a specific pattern
$ sed -n '/pattern_to_match/p' filename #Print only lines matching a specific pattern
$ sed 10q filename #Print the first 10 lines of a file
$ sed -n -e :a -e '$q;N;11,$D;ba' filename #Print the last 10 lines of a file
$ sed 's/pattern/replacement/n' filename #Replace the nth occurrence of a pattern in each line
$ sed 's/$/ text_to_append/' filename #Append text to the end of each line
$ sed 's/^/text_to_insert /' filename #Insert text at the beginning of each line
$ sed 's/^[ \t]*//' filename #Remove leading whitespace from each line
$ sed 's/[ \t]*$//' filename #Remove trailing whitespace from each line
$ sed -n 'start_line_number,end_line_numberp' filename #View only specified range of lines of a file

# awk commands
$ awk -F ',' '{print $1, $3}' filename.csv #Print specific columns from a CSV file
$ awk 'length($0) > 80' filename #Print lines longer than a specific length
$ awk 'END {print NR}' filename #Count the number of lines in a file
$ awk '/pattern_to_match/' filename #Print lines containing a specific pattern
$ awk '{sum += $2} END {print sum}' filename #Print the sum of values in a specific column
$ awk '{sum += $3; count++} END {print sum/count}' filename #Print the average of values in a specific column
$ awk 'max < $4 {max = $4} END {print max}' filename #Find the maximum value in a specific column
$ awk -F ':' '{print $3}' filename.txt #Extract and print a specific field based on a delimiter
$ awk '{$2 = "new_value"} 1' filename #Replace a specific field value with another value
$ awk 'NR==FNR{a[$1]=$2; next} ($1 in a) {print $0, a[$1]}' file1 file2 #Merge two files based on a common field
```

- recent logins and `sudo` usage view in `/var/log/auth.log`
- if the file `/var/log/auth.log` is missing, you’re probably using a minimal version of Ubuntu, use `sudo apt install rsyslog` and the file will be created

## Day 9 - Diving into networking

Ways of determining what ports are open on your server:
- `ss` - “socket status” is a standard utility, replacing the older `netstat`
- `nmap` - this “port scanner” won’t normally be installed by default

There are a wide range of options that can be used with _ss_, but first try: _ss -ltpn_

The output lines show which ports are open on which interfaces:

```bash
sudo ss -ltp
State   Recv-Q  Send-Q   Local Address:Port     Peer Address:Port  Process
LISTEN  0       4096     127.0.0.53%lo:53        0.0.0.0:*      users:(("systemd-resolve",pid=364,fd=13))
LISTEN  0       128            0.0.0.0:22           0.0.0.0:*      users:(("sshd",pid=625,fd=3))
LISTEN  0       128               [::]:22              [::]:*      users:(("sshd",pid=625,fd=4))
LISTEN  0       511                  *:80                *:*      users:(("apache2",pid=106630,fd=4),("apache2",pid=106629,fd=4),("apache2",pid=106627,fd=4))` 

#ports 80 and 22 open “to the world” on all local IP addresses - and port 53 (DNS) open only on a special local address
```

`nmap` works rather differently, actively probing 1,000 or more ports to check whether they’re open. It’s most famously used to scan remote machines, but it’s also very handy to check your own configuration, by scanning your server:

```bash
$ nmap localhost

Starting Nmap 5.21 ( http://nmap.org ) at 2013-03-17 02:18 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00042s latency).
Not shown: 998 closed ports
PORT STATE SERVICE
22/tcp open ssh
80/tcp open http

Nmap done: 1 IP address (1 host up) scanned in 0.08 seconds` 
```

- Port 22 is providing the _ssh_ service, which is how you’re connected, so that will be open. 
- Every open port is an increase in the “attack surface”, so it’s Best Practice to shut down services that you don’t need.
- “localhost” (127.0.0.1) is the loopback network device. Services “bound” only to this will only be available on this local machine. 
- We can use the `ip a` command to find the IP address of actual network card and then `nmap` that to see what’s actually exposed to others.

## Host firewall

The Linux kernel has built-in firewall functionality called “netfilter”. We configure and query this via various utilities, the most low-level of which are the `iptables` command, and the newer `nftables`, while we can use a more friendly `ufw` (“uncomplicated firewall”).

```bash
$ sudo iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination

#no firewalling - any traffic is accepted to anywhere
```

- to allow SSH usign ufw `sudo ufw allow ssh` (don’t forget to explicitly ALLOW `ssh`, or you’ll lose all contact with your server! If not allowed, the firewall assumes the port is DENIED by default)
- to disallow HTTP usign ufw `sudo ufw deny http` (after enabling, server is no longer accessible from the “outside”, all incoming traffic to the destination port of http/80 being DROPed)
- enable this with `sudo ufw enable` 
<br>

- Occasionally it may be reasonable to re-configure a service so that it’s provided on a non-standard port. This is particularly common advice for **ssh/22** and would be done by altering the configuration in `/etc/ssh/sshd_config`.
- Some call this “security by obscurity”, it does effectively eliminate attacks by opportunistic hackers, which is the main threat for most servers, but you need to remember it for all the rules and security tools you already have in place.

## Day 10 - Getting the computer to do your work for you

- `crontab -l` - list out your user crontab entry
- `sudo crontab -l` - list out root crontab entry
- there’s a system-wide crontab defined in `/etc/crontab`

```bash
# m h dom mon dow user  command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly

#explanation: Lines beginning with “#” are comments, so `# m h dom mon dow user command` defines the meanings of the columns. At 17mins after every hour, on every day, the credential for “root” will be used to run any scripts in the `/etc/cron.hourly` folder
```

- we have to look in those `/etc/cron.*` folders to see what’s actually scheduled.

```bash
$ ls -lr /etc/cron*
-rw-r--r-- 1 root root 1136 Mar 23  2022 /etc/crontab

/etc/cron.weekly:
total 4
-rwxr-xr-x 1 root root 1020 Mar 17  2022 man-db

/etc/cron.monthly:
total 0

/etc/cron.hourly:
total 0

/etc/cron.daily:
total 24
-rwxr-xr-x 1 root root 1330 Mar 17  2022 man-db
-rwxr-xr-x 1 root root  377 Jan 24  2022 logrotate
-rwxr-xr-x 1 root root  123 Dec  5  2021 dpkg
-rwxr-xr-x 1 root root 1478 Apr  8  2022 apt-compat
-rwxr-xr-x 1 root root  376 Nov 11  2019 apport
-rwxr-xr-x 1 root root  539 May  3  2023 apache2

/etc/cron.d:
total 4
-rw-r--r-- 1 root root 201 Jan  8  2022 e2scrub_all
```

- Each of these files is a script or a shortcut to a script to do some regular task, and they’re run in alphabetic order by `run-parts`. 
- commands `at` and `anacron` but are not likely to use them in a server.
- The `at` command is used in Unix-like operating systems to schedule tasks to be executed once, at a specified time in the future and it's particularly useful for tasks that need to occur at a specific time without the need for recurring scheduling.
- `anacron` is a utility used in Unix-like systems to execute commands periodically with a different approach compared to `cron` where it is designed to handle tasks that need to be executed regularly but not necessarily at precise intervals, and it's especially useful for tasks that might be missed if the system is powered off at certain times.
- `logrotate` is a utility found in Unix-like operating systems that manages log files by rotating, compressing, and optionally emailing them for archival or deletion. It's used to prevent log files from growing too large and consuming excessive disk space, while also ensuring that log data is retained for analysis or auditing purposes. It operates based on configuration files typically found in the `/etc/logrotate.d/` directory, where users can define rules for specific log files or directories.
- `less /var/log/syslog.1` to see how logs in server have been “rotated”
- all major Linux distributions now include “systemd”, which can also be used to run tasks at specific times via “timers”:

```bash
$ systemctl list-timers
NEXT                         LEFT          LAST                        PASSED       UNIT                           ACTIVATES
Mon 2024-03-25 20:22:07 CET  4h 33min left Sat 2024-03-23 08:14:58 CET 2 days ago   motd-news.timer                motd-news.service
Tue 2024-03-26 00:00:00 CET  8h left       n/a                         n/a          dpkg-db-backup.timer           dpkg-db-backup.service
Tue 2024-03-26 00:00:00 CET  8h left       Mon 2024-03-25 09:47:08 CET 6h ago       logrotate.timer                logrotate.service
Tue 2024-03-26 00:46:37 CET  8h left       Mon 2024-03-25 13:39:10 CET 2h 9min ago  apt-daily.timer                apt-daily.service
Tue 2024-03-26 05:13:58 CET  13h left      Mon 2024-03-25 14:18:20 CET 1h 30min ago man-db.timer                   man-db.service
Tue 2024-03-26 06:14:37 CET  14h left      Mon 2024-03-25 10:46:15 CET 5h 2min ago  apt-daily-upgrade.timer        apt-daily-upgrade.service
Tue 2024-03-26 09:52:10 CET  18h left      Mon 2024-03-25 09:52:10 CET 5h 56min ago update-notifier-download.timer update-notifier-download.service
Tue 2024-03-26 10:01:51 CET  18h left      Mon 2024-03-25 10:01:51 CET 5h 47min ago systemd-tmpfiles-clean.timer   systemd-tmpfiles-clean.service
Sat 2024-03-30 14:25:57 CET  4 days left   Wed 2024-03-20 17:10:51 CET 4 days ago   update-notifier-motd.timer     update-notifier-motd.service
Sun 2024-03-31 03:10:40 CEST 5 days left   Mon 2024-03-25 09:47:11 CET 6h ago       e2scrub_all.timer              e2scrub_all.service
Mon 2024-04-01 00:10:28 CEST 6 days left   Mon 2024-03-25 09:52:10 CET 5h 56min ago fstrim.timer                   fstrim.service
```

### Task

Schedule a job to _apt update_ and _apt upgrade_ everyday.

- Edit crontab file by running `crontab -e`
- Add the following line at the end of the file: `0 0 * * * apt update && apt upgrade -y`. This line tells `cron` to run `apt update` and `apt upgrade -y` every day at midnight. Save and exit the crontab editor.