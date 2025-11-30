# Over The Wire: Bandit

It's a beginner friendly wargame designed to teach practical Linux command-line and cybersecurity basics through hands-on challenges. Players connect to a remote server via SSH and progress through levels by discovering each level's password using standard tools, file system navigation, permissions, scripting, and common security techniques.

### Lvl 0-10

**Lvl 0**

The goal of this first level is only to connect to the remote server via ssh:

-on the host **bandit.labs.overthewire.org**

-on the port **2220**

-with the username **bandit0**

The command is:

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

**Lvl 1**

"The password for the next level is stored in a file called **readme** located in the home directory."
We can list all files present in the /home directory

```bash
bandit0@bandit:~$ ls -l
-rw-r----- 1 bandit1 bandit0 438 Oct 14 09:26 readme
```

and use the `cat` command to read the only file present here

```bash
bandit0@bandit:~$ cat readme 
The password you are looking for is: password_is_here
```

Now we can connect to the next lvl with the following command and the password we just get:

```bash
ssh bandit1@bandit.labs.overthewire.org -p 2220
```

**Lvl 2**

"The password for the next level is stored in a file called **-** located in the home directory"

Here, we use the same commands, `ls -l` to list all files then `cat` to read the file "-".

```bash
bandit1@bandit:~$ ls -l
-rw-r----- 1 bandit2 bandit1 33 Oct 14 09:26 -


bandit1@bandit:~$ cat ./-
password_is_here
```

**Lvl 3**

"The password for the next level is stored in a file called `--spaces in this filename--` located in the home directory"

Here, we use the same commands, `ls -l` to list all files then `cat` to read the file "-".

```bash
bandit2@bandit:~$ ls -l
-rw-r----- 1 bandit3 bandit2 33 Oct 14 09:26 --spaces in this filename--


bandit2@bandit:~$ cat ./--spaces\ in\ this\ filename-- 
password_is_here
```

**Lvl 4**

"The password for the next level is stored in a hidden file in the **inhere** directory."

`ls` to list all files and directory in the /home directory

`cd` to change directory, then list all files (with the flag `-a` to display any hidden files) in this directory and `cat` to read it.

```bash
bandit3@bandit:~$ ls -l
drwxr-xr-x 2 root root 4096 Oct 14 09:26 inhere


bandit3@bandit:~$ cd inhere/
bandit3@bandit:~/inhere$ 


bandit3@bandit:~/inhere$ ls -al
drwxr-xr-x 2 root    root    4096 Oct 14 09:26 .
drwxr-xr-x 3 root    root    4096 Oct 14 09:26 ..
-rw-r----- 1 bandit4 bandit3   33 Oct 14 09:26 ...Hiding-From-You

bandit3@bandit:~/inhere$ cat ./...Hiding-From-You 
password_is_here
```

**Lvl 5**

"The password for the next level is stored in the only human-readable
file in the **inhere** directory."

We go into the inhere/ directory and list all files

```bash
bandit4@bandit:~/inhere$ ls -l
-rw-r----- 1 bandit5 bandit4 33 Oct 14 09:26 -file00
-rw-r----- 1 bandit5 bandit4 33 Oct 14 09:26 -file01
-rw-r----- 1 bandit5 bandit4 33 Oct 14 09:26 -file02
-rw-r----- 1 bandit5 bandit4 33 Oct 14 09:26 -file03
-rw-r----- 1 bandit5 bandit4 33 Oct 14 09:26 -file04
-rw-r----- 1 bandit5 bandit4 33 Oct 14 09:26 -file05
-rw-r----- 1 bandit5 bandit4 33 Oct 14 09:26 -file06
-rw-r----- 1 bandit5 bandit4 33 Oct 14 09:26 -file07
-rw-r----- 1 bandit5 bandit4 33 Oct 14 09:26 -file08
-rw-r----- 1 bandit5 bandit4 33 Oct 14 09:26 -file09

```

There is 10 files, we have 2 options, brute force it by printing the content of all the files one-by-one, or use the `file` command to know the type to all the files.

```bash
bandit4@bandit:~/inhere$ file ./*
./-file00: data
./-file01: OpenPGP Public Key
./-file02: OpenPGP Public Key
./-file03: data
./-file04: data
./-file05: data
./-file06: data
./-file07: ASCII text
./-file08: data
./-file09: data
```

We can see that the -file07 is the only one with ASCII text, we just have to print it to get the password.

```bash
bandit4@bandit:~/inhere$ cat ./-file07 
password_is_here
```

**Lvl 6**

"The password for the next level is stored in a file somewhere under
the **inhere** directory and has all of the following properties:

- human-readable
- 1033 bytes in size
- not executable"

Once again let'go in the inhere directory and list.

```bash
bandit5@bandit:~/inhere$ ls -l
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere00
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere01
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere02
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere03
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere04
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere05
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere06
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere07
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere08
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere09
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere10
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere11
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere12
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere13
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere14
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere15
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere16
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere17
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere18
drwxr-x--- 2 root bandit5 4096 Oct 14 09:26 maybehere19
```

We are going to use the `find` command with flags to specify what we need.

`-type f`: only look at files

`-size 1033c`: look for 1033 bytes in size file

```bash
bandit5@bandit:~/inhere$ find . -type f -size 1033c 
./maybehere07/.file2


bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
password_is_here
```

**Lvl 7**

"The password for the next level is stored **somewhere on the
server** and has all of the following properties:

- owned by user bandit7
- owned by group bandit6
- 33 bytes in size"

We are going to use the `find` command with flags to specify what we need.

`-type f`: only look at files

`-size 33c`: look for 33 bytes in size file

`-user bandit7`

`-group bandit6`

And we add `2>/dev/null` to ignore all error message

```bash
find / -user bandit7 -group bandit6 -size 33c -type f 2>/dev/null
/var/lib/dpkg/info/bandit7.password

bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
password_is_here
```

**Lvl 8**

"The password for the next level is stored in the file **data.txt** next to the word **millionth**"

We have to print the file **data.txt**, use the pipe `|` to "pipe" the output of cat to grep. and use `grep` to filter the word millionth to get the password for next level.

```bash
bandit7@bandit:~$ ls
data.txt
bandit7@bandit:~$ cat data.txt | grep millionth
millionth	password_is_here
```

**Lvl 9**

"The password for the next level is stored in the file **data.txt** and is the only line of text that occurs only once"

We are going to use the `uniq` command with the flag `-u` to only sort the text that only occurs once. With the `sort` command which sort all the lines of a text file.

```bash
bandit8@bandit:~$ ls
data.txt
bandit8@bandit:~$ sort data.txt | uniq -u
password_is_here
```

**Lvl 10**

"The password for the next level is stored in the file **data.txt** in one of the few human-readable strings, preceded by several ‘=’
characters."

The command `strings` list human readable strings in a file. Then use  `grep ==` to get the password.

```bash
bandit9@bandit:~$ ls
data.txt
bandit9@bandit:~$ strings data.txt | grep ==
========== the
========== password
f\Z'========== is
========== password_is_here
```


