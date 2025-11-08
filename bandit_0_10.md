# Over The Wire Wargames - Bandit (0-10)

## Level 0

The goal of this level is for you to log into the game using SSH. The host to which you need to connect is bandit.labs.overthewire.org, on port 2220. The username is bandit0 and the password is bandit0. Once logged in, go to the Level 1 page to find out how to beat Level 1.

---

`$ ssh -p 2220 bandit0@bandit.labs.overthewire.org`

## Level 0 > Level 1

The password for the next level is stored in a file called readme located in the home directory. Use this password to log into bandit1 using SSH. Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game.

---

```bash
bandit0@bandit:~$ pwd
/home/bandit0

bandit0@bandit:~$ ls -l
total 4
-rw-r----- 1 bandit1 bandit0 438 Aug 15 13:15 readme

bandit0@bandit:~$ cat readme
Congratulations on your first steps into the bandit game!!
Please make sure you have read the rules at https://overthewire.org/rules/
If you are following a course, workshop, walkthrough or other educational activity,
please inform the instructor about the rules as well and encourage them to
contribute to the OverTheWire community so we can keep these games free!
The password you are looking for is: ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If

bandit0@bandit:~$ exit

$ sudo apt install sshpass

$ sshpass -p 'ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If' ssh -p 2220 bandit1@bandit.labs.overthewire.org

```

## Level 1 > Level 2

The password for the next level is stored in a file called - located in the home directory

---

```bash
bandit1@bandit:~$ ls -la
total 24
-rw-r-----   1 bandit2 bandit1   33 Aug 15 13:16 -
drwxr-xr-x   2 root    root    4096 Aug 15 13:16 .
drwxr-xr-x 150 root    root    4096 Aug 15 13:18 ..
-rw-r--r--   1 root    root     220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root    root    3851 Aug 15 13:09 .bashrc
-rw-r--r--   1 root    root     807 Mar 31  2024 .profile

bandit1@bandit:~$ cat ./-
263JGJPfgU6LtdEvgfWU1XP5yac29mFx

bandit1@bandit:~$ exit

$ sshpass -p '263JGJPfgU6LtdEvgfWU1XP5yac29mFx' ssh -p 2220 bandit2@bandit.labs.overthewire.org
```

## Level 2 > Level 3

The password for the next level is stored in a file called --spaces in this filename-- located in the home directory

---

```bash
bandit2@bandit:~$ ls
--spaces in this filename--

bandit2@bandit:~$ cat ./"--spaces in this filename--"
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx

bandit2@bandit:~$ exit

$ sshpass -p 'MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx' ssh -p 2220 bandit3@bandit.labs.overthewire.org
```

## Level 3 > Level 4

The password for the next level is stored in a hidden file in the inhere directory.

---

```bash
bandit3@bandit:~$ ls -la inhere/
total 12
drwxr-xr-x 2 root    root    4096 Aug 15 13:16 .
drwxr-xr-x 3 root    root    4096 Aug 15 13:16 ..
-rw-r----- 1 bandit4 bandit3   33 Aug 15 13:16 ...Hiding-From-You

bandit3@bandit:~$ cat inhere/...Hiding-From-You
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ

bandit3@bandit:~$ exit

$ sshpass -p '2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ' ssh -p 2220 bandit4@bandit.labs.overthewire.org
```

## Level 4 > Level 5 ****

The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command.

---

```bash
bandit4@bandit:~$ file inhere/*
inhere/-file00: data
inhere/-file01: data
inhere/-file02: data
inhere/-file03: data
inhere/-file04: data
inhere/-file05: data
inhere/-file06: data
inhere/-file07: ASCII text
inhere/-file08: data
inhere/-file09: data

bandit4@bandit:~$ cat inhere/-file07
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw

bandit4@bandit:~$ exit

$ sshpass -p '4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw' ssh -p 2220 bandit5@bandit.labs.overthewire.org
```

## Level 5 > Level 6

The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:
- human-readable
- 1033 bytes in size
- not executable

---

```bash
bandit5@bandit:~$ find . -type f -readable -size 1033c ! -executable
./inhere/maybehere07/.file2

bandit5@bandit:~$ cat inhere/maybehere07/.file2
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG

$ sshpass -p 'HWasnPhtq9AVKe0dmk45nxy20cvUa6EG' ssh -p 2220 bandit6@bandit.labs.overthewire.org
```

## Level 6 > Level 7

The password for the next level is stored somewhere on the server and has all of the following properties:
- owned by user bandit7
- owned by group bandit6
- 33 bytes in size

---

```bash
bandit6@bandit:~$ find / -type f -user bandit7 -group bandit6 -size 33c 2> /dev/null
/var/lib/dpkg/info/bandit7.password

bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj

$ sshpass -p 'morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj' ssh -p 2220 bandit7@bandit.labs.overthewire.org
```

## Level 7 > Level 8

The password for the next level is stored in the file data.txt next to the word millionth

---

```bash
bandit7@bandit:~$ ls
data.txt

bandit7@bandit:~$ grep "millionth" data.txt
millionth       dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc

bandit7@bandit:~$ exit

$ sshpass -p 'dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc' ssh -p 2220 bandit8@bandit.labs.overthewire.org
```

## Level 8 > Level 9

The password for the next level is stored in the file data.txt and is the only line of text that occurs only once

---

```bash
bandit8@bandit:~$ sort data.txt | uniq -u
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM

bandit8@bandit:~$ exit

$ sshpass -p '4CKMh1JI91bUIZZPXDqGanal4xvAg0JM' ssh -p 2220 bandit9@bandit.labs.overthewire.org
```

## Level 9 > Level 10

The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters.

---

```bash
bandit9@bandit:~$ ls
data.txt

bandit9@bandit:~$ strings data.txt | grep '=='
========== the
========== password
Q========== is%
>u`9J========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey

bandit9@bandit:~$ exit

$ sshpass -p 'FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey' ssh -p 2220 bandit10@bandit.labs.overthewire.org
```

## Level 10 > Level 11

The password for the next level is stored in the file data.txt, which contains base64 encoded data

---

```bash
bandit10@bandit:~$ ls
data.txt

bandit10@bandit:~$ cat data.txt
VGhlIHBhc3N3b3JkIGlzIGR0UjE3M2ZaS2IwUlJzREZTR3NnMlJXbnBOVmozcVJyCg==

bandit10@bandit:~$ base64 -d data.txt
The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr

bandit10@bandit:~$ exit

$ sshpass -p 'dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr' ssh -p 2220 bandit11@bandit.labs.overthewire.org
```