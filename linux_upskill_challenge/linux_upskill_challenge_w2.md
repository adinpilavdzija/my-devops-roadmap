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