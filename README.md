# HTB-Netmon
```bash
└─$ sudo nmap -A -T4 --min-rate 1000 netmon.htb
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-18 17:29 -0300
Nmap scan report for netmon.htb (10.129.230.176)
Host is up (0.12s latency).
Not shown: 994 closed tcp ports (reset)
PORT     STATE SERVICE      VERSION
21/tcp   open  ftp          Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 02-03-19  12:18AM                 1024 .rnd
| 02-25-19  10:15PM       <DIR>          inetpub
| 07-16-16  09:18AM       <DIR>          PerfLogs
| 02-25-19  10:56PM       <DIR>          Program Files
| 02-03-19  12:28AM       <DIR>          Program Files (x86)
| 02-03-19  08:08AM       <DIR>          Users
|_11-10-23  10:20AM       <DIR>          Windows
| ftp-syst: 
|_  SYST: Windows_NT
80/tcp   open  http         Indy httpd 18.1.37.13946 (Paessler PRTG bandwidth monitor)
|_http-server-header: PRTG/18.1.37.13946
|_http-trane-info: Problem with XML parsing of /evox/about
| http-title: Welcome | PRTG Network Monitor (NETMON)
|_Requested resource was /index.htm
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
5985/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Device type: general purpose
Running: Microsoft Windows 2016
OS CPE: cpe:/o:microsoft:windows_server_2016
OS details: Microsoft Windows Server 2016
Network Distance: 2 hops
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2026-06-18T20:29:50
|_  start_date: 2026-06-18T20:22:23
| smb-security-mode: 
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   207.40 ms 10.10.16.1
2   207.66 ms netmon.htb (10.129.230.176)
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.38 seconds
```

The nmap report alreadi shows us something very important. We can scan FTP anonymously. Before we do that, let's check the website:
<img width="1625" height="765" alt="image" src="https://github.com/user-attachments/assets/dab59012-a925-4fd7-9b0d-91e7ac2eeddb" />

Now if your first instinct was trying some credential here like admin/admin this is correct but they wont work. For now, let's login anonymously with the FTP client and see where we can get, just use anonymous/guest as credendials:
<img width="610" height="327" alt="image" src="https://github.com/user-attachments/assets/49cb93a4-cfea-4771-8c3a-c66094742d85" />

Navigating ftp is easy, very similat to bash and powershell. If we roam around for a bit we can find our user.txt the first flag.
<img width="1893" height="137" alt="image" src="https://github.com/user-attachments/assets/0c29114e-76f0-4c1a-8e7c-d656d0dc313a" />

Navigate to ProgramData/PRTG Network Monitor/ and get the file PRTG Configuration.old.bak. You should have enumerated all of them but you'll only find somehtin inside the .bak file. That is credentials prtgadmin/PrTg@dmin2018 that can be used ath the website at port 80.
<img width="851" height="180" alt="image" src="https://github.com/user-attachments/assets/b506b45c-3e6f-4b96-9886-9266da840bba" />

Alas it does not work. but if we tamper with the credential for a bit and change de year from 2018 to 2019 we can get access:
<img width="1918" height="731" alt="image" src="https://github.com/user-attachments/assets/403bec4e-cee1-4113-8797-2ca72ae5f960" />

Bottom left of the page we can see the version number of the software 18.1.37.13946 if we google this version number we can get to  this Poc https://github.com/A1vinSmith/CVE-2018-9276 trigger it like so:
<img width="1312" height="678" alt="image" src="https://github.com/user-attachments/assets/354e462e-395a-40d7-89f7-c24d1a63a83d" />

And get system webshell:
<img width="632" height="151" alt="image" src="https://github.com/user-attachments/assets/18dc426d-329c-4a34-b21b-fb26603cd229" />

<img width="755" height="237" alt="image" src="https://github.com/user-attachments/assets/ef211ef6-39f2-4891-a2cf-47f5ef70237d" />






