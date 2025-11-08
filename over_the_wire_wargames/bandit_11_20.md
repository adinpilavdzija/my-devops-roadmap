# Over The Wire Wargames - Bandit (11-20)

## Level 11 > Level 12

The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

---

```bash
bandit11@bandit:~$ ls
data.txt

bandit11@bandit:~$ cat data.txt
Gur cnffjbeq vf 7k16JArUVv5LxVuJfsSVdbbtaHGlw9D4

bandit11@bandit:~$ cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
The password is 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4

bandit11@bandit:~$ exit

$ sshpass -p '7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4' ssh -p 2220 bandit12@bandit.labs.overthewire.org
```

## Level 12 > Level 13

The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work. Use mkdir with a hard to guess directory name. Or better, use the command “mktemp -d”. Then copy the datafile using cp, and rename it using mv (read the manpages!)

---

```bash
bandit12@bandit:~$ ls
data.txt

bandit12@bandit:~$ cat data.txt
00000000: 1f8b 0808 0933 9f68 0203 6461 7461 322e  .....3.h..data2.
00000010: 6269 6e00 0148 02b7 fd42 5a68 3931 4159  bin..H...BZh91AY
00000020: 2653 59be 9d9d 9600 001f ffff fe7f fbcf  &SY.............
00000030: af7f 9eff f7ee ffdf bff7 fef7 ddbe 9db7  ................
00000040: bf9f 9f5f ca6f fffe d6fb feff b001 3ab3  ..._.o........:.
00000050: 0403 40d0 0000 00d0 01a0 03d4 0000 0346  ..@............F
...

bandit12@bandit:~$ mktemp -d
/tmp/tmp.nwTFgArpng

bandit12@bandit:~$ cd /tmp/tmp.nwTFgArpng

bandit12@bandit:/tmp/tmp.nwTFgArpng$ cp /home/bandit12/data.txt .

bandit12@bandit:/tmp/tmp.nwTFgArpng$ ls
data.txt

bandit12@bandit:/tmp/tmp.nwTFgArpng$ xxd -r data.txt > data2.txt

bandit12@bandit:/tmp/tmp.nwTFgArpng$ file data2.txt
data2.txt: gzip compressed data, was "data2.bin", last modified: Fri Aug 15 13:15:53 2025, max compression, from Unix, original size modulo 2^32 584

bandit12@bandit:/tmp/tmp.nwTFgArpng$ mv data2.txt data2.gz

bandit12@bandit:/tmp/tmp.nwTFgArpng$ gzip -d data2.gz

bandit12@bandit:/tmp/tmp.nwTFgArpng$ ls -l
total 8
-rw-rw-r-- 1 bandit12 bandit12  584 Sep 16 12:07 data2
-rw-r----- 1 bandit12 bandit12 2645 Sep 16 12:05 data.txt

bandit12@bandit:/tmp/tmp.nwTFgArpng$ file data2
data2: bzip2 compressed data, block size = 900k

bandit12@bandit:/tmp/tmp.nwTFgArpng$ mv data2 data2.bz

bandit12@bandit:/tmp/tmp.nwTFgArpng$ bzip2 -d data2.bz

bandit12@bandit:/tmp/tmp.nwTFgArpng$ ls
data2  data.txt

bandit12@bandit:/tmp/tmp.nwTFgArpng$ file data2
data2: gzip compressed data, was "data4.bin", last modified: Fri Aug 15 13:15:53 2025, max compression, from Unix, original size modulo 2^32 20480

bandit12@bandit:/tmp/tmp.nwTFgArpng$ mv data2 data2.gz

bandit12@bandit:/tmp/tmp.nwTFgArpng$ gzip -d data2.gz

bandit12@bandit:/tmp/tmp.nwTFgArpng$ ls
data2  data.txt

bandit12@bandit:/tmp/tmp.nwTFgArpng$ file data2
data2: POSIX tar archive (GNU)

bandit12@bandit:/tmp/tmp.nwTFgArpng$ tar -xf data2.tar

bandit12@bandit:/tmp/tmp.nwTFgArpng$ ls
data2.tar  data5.bin  data.txt

bandit12@bandit:/tmp/tmp.nwTFgArpng$ file data5.bin
data5.bin: POSIX tar archive (GNU)

bandit12@bandit:/tmp/tmp.nwTFgArpng$ mv data5.bin data5.tar
bandit12@bandit:/tmp/tmp.nwTFgArpng$ tar -xf data5.tar
bandit12@bandit:/tmp/tmp.nwTFgArpng$ ls
data2.tar  data5.tar  data6.bin  data.txt
bandit12@bandit:/tmp/tmp.nwTFgArpng$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k

bandit12@bandit:/tmp/tmp.nwTFgArpng$ mv data6.bin data6.bz
bandit12@bandit:/tmp/tmp.nwTFgArpng$ bzip -d data6.bz
Command 'bzip' not found, but there are 21 similar ones.
bandit12@bandit:/tmp/tmp.nwTFgArpng$ bzip2 -d data6.bz
bandit12@bandit:/tmp/tmp.nwTFgArpng$ ls
data2.tar  data5.tar  data6  data.txt
bandit12@bandit:/tmp/tmp.nwTFgArpng$ file data6
data6: POSIX tar archive (GNU)

bandit12@bandit:/tmp/tmp.nwTFgArpng$ mv data6 data6.tar
bandit12@bandit:/tmp/tmp.nwTFgArpng$ tar -xf data6.tar
bandit12@bandit:/tmp/tmp.nwTFgArpng$ ls
data2.tar  data5.tar  data6.tar  data8.bin  data.txt
bandit12@bandit:/tmp/tmp.nwTFgArpng$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Fri Aug 15 13:15:53 2025, max compression, from Unix, original size modulo 2^32 49

bandit12@bandit:/tmp/tmp.nwTFgArpng$ mv data8.bin data8.gz
bandit12@bandit:/tmp/tmp.nwTFgArpng$ gzip -d data8.gz
bandit12@bandit:/tmp/tmp.nwTFgArpng$ ls
data2.tar  data5.tar  data6.tar  data8  data.txt
bandit12@bandit:/tmp/tmp.nwTFgArpng$ file data8
data8: ASCII text
bandit12@bandit:/tmp/tmp.nwTFgArpng$ cat data8
The password is FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
bandit12@bandit:/tmp/tmp.nwTFgArpng$ exit

$ sshpass -p 'FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn' ssh -p 2220 bandit13@bandit.labs.overthewire.org
```

## Level 13 > Level 14

The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Note: localhost is a hostname that refers to the machine you are working on

---

```bash
bandit13@bandit:~$ ls
sshkey.private

bandit13@bandit:~$ ssh -i ~/sshkey.private -p 2220 bandit14@bandit.labs.overthewire.org

bandit14@bandit:~$ cat /etc/bandit_pass/bandit14
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS

bandit14@bandit:~$ exit
bandit13@bandit:~$ exit

$ sshpass -p 'MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS' ssh -p 2220 bandit14@bandit.labs.overthewire.org
```

## Level 14 > Level 15

The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

---

```bash
bandit14@bandit:~$ echo 'MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS' | nc localhost 30000
Correct!
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo

bandit14@bandit:~$ exit

$ sshpass -p '8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo' ssh -p 2220 bandit15@bandit.labs.overthewire.org
```

## Level 15 > Level 16

The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL/TLS encryption.

Helpful note: Getting “DONE”, “RENEGOTIATING” or “KEYUPDATE”? Read the “CONNECTED COMMANDS” section in the manpage.

---

```bash
bandit15@bandit:~$ openssl s_client localhost:30001
CONNECTED(00000003)
---
...
read R BLOCK
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
Correct!
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
...
closed

bandit15@bandit:~$ exit

$ sshpass -p 'kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx' ssh -p 2220 bandit16@bandit.labs.overthewire.org
```

## Level 16 > Level 17

The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000. First find out which of these ports have a server listening on them. Then find out which of those speak SSL/TLS and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

---

```bash
bandit16@bandit:~$ nmap -sV -T4 -p 31000-32000 localhost
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-16 13:44 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00010s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT      STATE SERVICE     VERSION
31046/tcp open  echo
31518/tcp open  ssl/echo
31691/tcp open  echo
31790/tcp open  ssl/unknown
31960/tcp open  echo
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port31790-TCP:V=7.94SVN%T=SSL%I=7%D=9/16%Time=68C969B6%P=x86_64-pc-linu
SF:x-gnu%r(GenericLines,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x2
SF:0current\x20password\.\n")%r(GetRequest,32,"Wrong!\x20Please\x20enter\x
SF:20the\x20correct\x20current\x20password\.\n")%r(HTTPOptions,32,"Wrong!\
SF:x20Please\x20enter\x20the\x20correct\x20current\x20password\.\n")%r(RTS
SF:PRequest,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20
SF:password\.\n")%r(Help,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x
SF:20current\x20password\.\n")%r(FourOhFourRequest,32,"Wrong!\x20Please\x2
SF:0enter\x20the\x20correct\x20current\x20password\.\n")%r(LPDString,32,"W
SF:rong!\x20Please\x20enter\x20the\x20correct\x20current\x20password\.\n")
SF:%r(SIPOptions,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x20curren
SF:t\x20password\.\n");
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 147.67 seconds

bandit16@bandit:~$ openssl s_client localhost:31790
...
read R BLOCK
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
KEYUPDATE
^C

# When used interactively (which means neither -quiet nor -ign_eof have been given), then certain commands are also recognized which perform special operations. These commands are a letter which must appear at the start of a line. They are listed below.
# Q - End the current SSL connection and exit.
# R - Renegotiate the SSL session (TLSv1.2 and below only).
# k - Send a key update message to the server (TLSv1.3 only)
# K - Send a key update message to the server and request one back (TLSv1.3 only)
# So if the password starts with one of this keystorekes, it will trigger an action. When used with the -quiet it won't be the case.

bandit16@bandit:~$ openssl s_client --connect localhost:31790 -ign_eof
CONNECTED(00000003)
...
read R BLOCK
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
closed

bandit16@bandit:~$ mkdir /tmp/sshkey

bandit16@bandit:~$ cat > /tmp/sshkey/private.key
# copy the key

bandit16@bandit:~$ ls -l /tmp/sshkey/private.key
-rw-rw-r-- 1 bandit16 bandit16 1675 Sep 17 08:12 /tmp/sshkey/private.key

bandit16@bandit:~$ chmod 400 /tmp/sshkey/private.key

bandit16@bandit:~$ ls -l /tmp/sshkey/private.key
-r-------- 1 bandit16 bandit16 1675 Sep 17 08:12 /tmp/sshkey/private.key

bandit16@bandit:~$ ssh -i /tmp/sshkey/private.key -p 2220 bandit17@bandit.labs.overthewire.org

bandit17@bandit:~$ cat /etc/bandit_pass/bandit17
EReVavePLFHtFlFsjn3hyzMlvSuSAcRD
```

## Level 17 > Level 18

There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new

NOTE: if you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, bandit19

---

```bash
bandit17@bandit:~$ ls
passwords.new  passwords.old

bandit17@bandit:~$ diff passwords.new passwords.old
42c42
< x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
---
> CgmS55GVlEKTgx8xpW8HuWnHlBKP924b

bandit17@bandit:~$ exit

$ sshpass -p 'x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO' ssh -p 2220 bandit18@bandit.labs.overthewire.org
```

## Level 18 > Level 19

The password for the next level is stored in a file readme in the homedirectory. Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.

---

```bash
$ sshpass -p 'x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO' ssh -p 2220 bandit18@bandit.labs.overthewire.org "cat ~/readme"
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|


                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

backend: gibson-1
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
```

or

```bash
$ sshpass -p 'x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO' ssh -p 2220 bandit18@bandit.labs.overthewire.org "tail -3 ~/.bashrc"
...
fi
echo 'Byebye !'
exit 0

# line 'exit 0' is making problems but we don't have permissions to edit .bashrc file

$ sshpass -p 'x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO' ssh -p 2220 bandit18@bandit.labs.overthewire.org "cat /etc/shells"
# /etc/shells: valid login shells
/bin/sh
/usr/bin/sh
/bin/bash
/usr/bin/bash
/bin/rbash
/usr/bin/rbash
/usr/bin/dash
/usr/bin/screen
/usr/bin/tmux
/usr/bin/showtext

$ sshpass -p 'x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO' ssh -p 2220 bandit18@bandit.labs.overthewire.org -t "/bin/sh"

$ ls
readme

$ cat readme
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8

$ exit

$ sshpass -p 'cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8' ssh -p 2220 bandit19@bandit.labs.overthewire.org
```

## Level 19 > Level 20

To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.

---

```bash
bandit19@bandit:~$ ls -l
total 16
-rwsr-x--- 1 bandit20 bandit19 14884 Aug 15 13:16 bandit20-do

bandit19@bandit:~$ ./bandit20-do
Run a command as another user.
  Example: ./bandit20-do id

bandit19@bandit:~$ id
uid=11019(bandit19) gid=11019(bandit19) groups=11019(bandit19)

bandit19@bandit:~$ ./bandit20-do id
uid=11019(bandit19) gid=11019(bandit19) euid=11020(bandit20) groups=11019(bandit19)

bandit19@bandit:~$ cat /etc/bandit_pass/bandit20
cat: /etc/bandit_pass/bandit20: Permission denied

bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO

bandit19@bandit:~$ exit

$ sshpass -p '0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO' ssh -p 2220 bandit20@bandit.labs.overthewire.org
```

## Level 20 > Level 21

There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

NOTE: Try connecting to your own network daemon to see if it works as you think

---

```bash
bandit20@bandit:~$ ls -l
total 16
-rwsr-x--- 1 bandit21 bandit20 15608 Aug 15 13:16 suconnect

bandit20@bandit:~$ ./suconnect
Usage: ./suconnect <portnumber>
This program will connect to the given port on localhost using TCP. If it receives the correct password from the other side, the next password is transmitted back.

bandit20@bandit:~$ ss -tulpn
# check active ports

bandit20@bandit:~$ echo '0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO' | nc -lp 10010 &
[1] 2525005

bandit20@bandit:~$ jobs
[1]+  Running                 echo '0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO' | nc -lp 10010 &

bandit20@bandit:~$ ./suconnect 10010
Read: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
Password matches, sending next password
EeoULMCra2q0dSkYj561DX7s1CpBuOBt
[1]+  Done                    echo '0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO' | nc -lp 10010

bandit20@bandit:~$ exit

$ sshpass -p 'EeoULMCra2q0dSkYj561DX7s1CpBuOBt' ssh -p 2220 bandit21@bandit.labs.overthewire.org
```