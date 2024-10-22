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

* Dirbusting streamio.htb

```text
[21:41:37] 301 -  147B  - /js  ->  https://streamio.htb/js/                 
[21:41:37] 403 -  312B  - /%2e%2e//google.com                               
[21:41:37] 403 -  312B  - /.%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd             
[21:41:52] 403 -  312B  - /\..\..\..\..\..\..\..\..\..\etc\passwd           
[21:41:54] 200 -    8KB - /about.php                                        
[21:41:57] 301 -  150B  - /ADMIN  ->  https://streamio.htb/ADMIN/           
[21:41:57] 301 -  150B  - /Admin  ->  https://streamio.htb/Admin/           
[21:41:57] 301 -  150B  - /admin  ->  https://streamio.htb/admin/           
[21:41:58] 403 -   18B  - /admin/                                           
[21:41:58] 403 -   18B  - /Admin/                                           
[21:41:59] 403 -   18B  - /admin/index.php                                  
[21:42:25] 403 -  312B  - /cgi-bin/.%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd     
[21:42:30] 200 -    6KB - /contact.php                                      
[21:42:31] 301 -  148B  - /css  ->  https://streamio.htb/css/               
[21:42:36] 400 -    3KB - /docpicker/internal_proxy/https/127.0.0.1:9043/ibm/console
[21:42:41] 200 -    1KB - /favicon.ico                                      
[21:42:42] 301 -  150B  - /fonts  ->  https://streamio.htb/fonts/           
[21:42:50] 301 -  151B  - /images  ->  https://streamio.htb/images/         
[21:42:50] 403 -    1KB - /images/
[21:42:54] 403 -    1KB - /js/                                              
[21:42:59] 200 -    4KB - /login.php                                        
[21:43:00] 302 -    0B  - /logout.php  ->  https://streamio.htb/            
[21:43:23] 200 -    4KB - /register.php                                     
[21:43:38] 403 -    2KB - /Trace.axd
```
