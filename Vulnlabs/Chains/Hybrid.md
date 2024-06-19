# KEY TAKE AWAYS
* NFS
* ADCS

# NMAP

* DC-01 SCAN

```
PORT      STATE SERVICE       REASON  VERSION
53/tcp    open  domain        syn-ack Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack Microsoft Windows Kerberos (server time: 2024-06-17 04:33:35Z)
135/tcp   open  msrpc         syn-ack Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack Microsoft Windows Active Directory LDAP (Domain: hybrid.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.hybrid.vl
445/tcp   open  microsoft-ds? syn-ack
464/tcp   open  kpasswd5?     syn-ack
593/tcp   open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack Microsoft Windows Active Directory LDAP (Domain: hybrid.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.hybrid.vl
3268/tcp  open  ldap          syn-ack Microsoft Windows Active Directory LDAP (Domain: hybrid.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.hybrid.vl
3269/tcp  open  ssl/ldap      syn-ack Microsoft Windows Active Directory LDAP (Domain: hybrid.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.hybrid.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc01.hybrid.vl
| Issuer: commonName=hybrid-DC01-CA/domainComponent=hybrid
3389/tcp  open  ms-wbt-server syn-ack Microsoft Terminal Services
| ssl-cert: Subject: commonName=dc01.hybrid.vl
| Issuer: commonName=dc01.hybrid.vl
| rdp-ntlm-info: 
|   Target_Name: HYBRID
|   NetBIOS_Domain_Name: HYBRID
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: hybrid.vl
|   DNS_Computer_Name: dc01.hybrid.vl
|   DNS_Tree_Name: hybrid.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-06-17T04:34:33+00:00
5985/tcp  open  http          syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack .NET Message Framing
49664/tcp open  msrpc         syn-ack Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack Microsoft Windows RPC
54871/tcp open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0
59425/tcp open  msrpc         syn-ack Microsoft Windows RPC
59439/tcp open  msrpc         syn-ack Microsoft Windows RPC
59446/tcp open  msrpc         syn-ack Microsoft Windows RPC
59461/tcp open  msrpc         syn-ack Microsoft Windows RPC
62491/tcp open  msrpc         syn-ack Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows
```

* MAIL-01 SCAN

```
22/tcp    open  ssh      syn-ack OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 60:bc:22:26:78:3c:b4:e0:6b:ea:aa:1e:c1:62:5d:de (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBLl+dlYZiceVG9g/8U0XSs4cWJ/6msyXPI/mORr9T9i4oQA66eYZSYwrxEwYwDZvrhXu7foZtEdeu3u6uSUnl4k=
|   256 a3:b5:d8:61:06:e6:3a:41:88:45:e3:52:03:d2:23:1b (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILJyLrRGDcNfa9bQg1dhsV/CPQapLeRxpWJDUOQ+MI1c
25/tcp    open  smtp     syn-ack Postfix smtpd
|_smtp-commands: mail01.hybrid.vl, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, AUTH PLAIN LOGIN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, CHUNKING
80/tcp    open  http     syn-ack nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-title: Redirecting...
|_http-server-header: nginx/1.18.0 (Ubuntu)
110/tcp   open  pop3     syn-ack Dovecot pop3d
|_ssl-date: TLS randomness does not represent time
111/tcp   open  rpcbind  syn-ack 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      34633/udp   mountd
|   100005  1,2,3      51585/tcp6  mountd
|   100005  1,2,3      53453/udp6  mountd
|   100005  1,2,3      54905/tcp   mountd
|   100021  1,3,4      33153/tcp6  nlockmgr
|   100021  1,3,4      42981/tcp   nlockmgr
|   100021  1,3,4      51205/udp6  nlockmgr
|   100021  1,3,4      60133/udp   nlockmgr
|   100024  1          36356/udp6  status
|   100024  1          54811/tcp6  status
|   100024  1          58397/tcp   status
|   100024  1          59133/udp   status
|   100227  3           2049/tcp   nfs_acl
|_  100227  3           2049/tcp6  nfs_acl
143/tcp   open  imap     syn-ack Dovecot imapd (Ubuntu)
|_imap-capabilities: more have LITERAL+ capabilities IMAP4rev1 ENABLE LOGINDISABLEDA0001 IDLE listed STARTTLS OK SASL-IR ID post-login Pre-login LOGIN-REFERRALS
|_ssl-date: TLS randomness does not represent time
587/tcp   open  smtp     syn-ack Postfix smtpd
|_smtp-commands: mail01.hybrid.vl, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, AUTH PLAIN LOGIN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, CHUNKING
993/tcp   open  ssl/imap syn-ack Dovecot imapd (Ubuntu)
|_imap-capabilities: more AUTH=LOGINA0001 LITERAL+ capabilities IMAP4rev1 ENABLE have IDLE AUTH=PLAIN OK listed SASL-IR ID post-login Pre-login LOGIN-REFERRALS
|_ssl-date: TLS randomness does not represent time
995/tcp   open  ssl/pop3 syn-ack Dovecot pop3d
| ssl-cert: Subject: commonName=mail01
|_ssl-date: TLS randomness does not represent time
2049/tcp  open  nfs_acl  syn-ack 3 (RPC #100227)
35179/tcp open  mountd   syn-ack 1-3 (RPC #100005)
42981/tcp open  nlockmgr syn-ack 1-4 (RPC #100021)
52437/tcp open  mountd   syn-ack 1-3 (RPC #100005)
54905/tcp open  mountd   syn-ack 1-3 (RPC #100005)
58397/tcp open  status   syn-ack 1 (RPC #100024)
Service Info: Host:  mail01.hybrid.vl; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

# NFS MOUNT

* Mounting the NFS share reveals creds

```
showmount -e $IP
```

![image](https://github.com/reneanto/write-ups/assets/44943249/a95ae030-381c-418b-9904-83fc22a5afb0)

```
sudo mkdir /mnt/nfs
sudo mount -t nfs $IP:/opt/share /mnt/nfs
cd /mnt/nfs
ls
tar -xvf backup.tar.gz
cd /etc/dovecot
```

![image](https://github.com/reneanto/write-ups/assets/44943249/f5ed164a-dce5-426d-8891-9f7fcbd66476)

# Roundcube markasjunk exploit

* as discovered in nmap scan, we can log on to the roundcube web server using the credentials found via the NFS share
* upon logging in we can find that the markasjunk plugin is installed and as per the blog https://cyberthint.io/roundcube-markasjunk-command-injection-vulnerability/ we can proceed to exploit it

![image](https://github.com/reneanto/write-ups/assets/44943249/bac27938-c0dd-4b35-8493-6771ae61de94)

![image](https://github.com/reneanto/write-ups/assets/44943249/8c83a45e-1285-45f0-bd7b-1d8cd5e3b288)


