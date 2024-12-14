# Unrested

### pre-requisite

* Credential provided for an account in zabbix  `matthew / 96qzn0h2e1k3`

## NMAP

* *Rustscan*

```console
PORT      STATE SERVICE        REASON
22/tcp    open  ssh            syn-ack ttl 63
80/tcp    open  http           syn-ack ttl 63
10050/tcp open  zabbix-agent   syn-ack ttl 63
10051/tcp open  zabbix-trapper syn-ack ttl 63
```

* *NMAP*

```console
PORT      STATE SERVICE             REASON         VERSION
22/tcp    open  ssh                 syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBJ+m7rYl1vRtnm789pH3IRhxI4CNCANVj+N5kovboNzcw9vHsBwvPX3KYA3cxGbKiA0VqbKRpOHnpsMuHEXEVJc=
|   256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOtuEdoYxTohG80Bo6YCqSzUY9+qbnAFnhsk4yAZNqhM
80/tcp    open  http                syn-ack ttl 63 Apache httpd 2.4.52 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
| http-methods: 
|_  Supported Methods: POST OPTIONS HEAD GET
|_http-server-header: Apache/2.4.52 (Ubuntu)
10050/tcp open  zabbix-agent?       syn-ack ttl 63
10051/tcp open  ssl/zabbix-trapper? syn-ack ttl 63
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## Web

* port 80 seems to be running zabbix

![zabbix](/HTB/Machines/images/unrested-web.png)
* Post logging in it gives us the version of the Zabbix which is 7.0.0
* Create a token via User settings
* As per https://github.com/compr00t/CVE-2024-42327 we can further try to exploit the SQLi