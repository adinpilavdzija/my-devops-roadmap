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