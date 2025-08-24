
## NMAP

* Rustscan results

```console

PORT     STATE SERVICE       REASON
80/tcp   open  http          syn-ack ttl 127
445/tcp  open  microsoft-ds  syn-ack ttl 127
3000/tcp open  ppp           syn-ack ttl 127
3389/tcp open  ms-wbt-server syn-ack ttl 127
```

* NMAP results

```console
PORT     STATE SERVICE       REASON          VERSION
80/tcp   open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
445/tcp  open  microsoft-ds? syn-ack ttl 127
3000/tcp open  http          syn-ack ttl 127 Golang net/http server
| http-methods: 
|_  Supported Methods: HEAD GET
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 404 Not Found
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Content-Type: text/plain;charset=utf-8
|     Set-Cookie: i_like_gitea=a8d417677370b37f; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=OmRtt9YqBiJkaBKpmr8sRfF1c3s6MTc1NjAzODg0MzAyMjA0MzAwMA; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: SAMEORIGIN
|     Date: Sun, 24 Aug 2025 12:34:03 GMT
|     Content-Length: 11
|     found.
|   GenericLines, Help, LPDString, RTSPRequest, SIPOptions, SSLSessionReq: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   HTTPOptions: 
|     HTTP/1.0 405 Method Not Allowed
|     Allow: HEAD
|     Allow: GET
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Set-Cookie: i_like_gitea=4ea225f9a645e73d; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=NhKJSUu4YgEt9U1lvKOS45k7C8s6MTc1NjAzODgyNzU4OTY2NDcwMA; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Sun, 24 Aug 2025 12:33:47 GMT
|_    Content-Length: 0
3389/tcp open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
|_ssl-date: 2025-08-24T12:34:59+00:00; +43s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: LOCK
|   NetBIOS_Domain_Name: LOCK
|   NetBIOS_Computer_Name: LOCK
|   DNS_Domain_Name: Lock
|   DNS_Computer_Name: Lock
|   Product_Version: 10.0.20348
|_  System_Time: 2025-08-24T12:34:09+00:00
| ssl-cert: Subject: commonName=Lock
| Issuer: commonName=Lock
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-04-15T00:34:47
| Not valid after:  2025-10-15T00:34:47
| MD5:   a26b:6f48:753d:e8cf:55e6:dc59:f8db:a2a9
| SHA-1: 1f19:4a17:9f9f:ca14:3202:9866:2228:b734:bbaa:e3ed
```