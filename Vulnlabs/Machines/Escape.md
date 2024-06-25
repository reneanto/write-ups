# KEY TAKE AWAYS

* KIOSK account break out


# NMAP

```
PORT     STATE SERVICE       REASON  VERSION
3389/tcp open  ms-wbt-server syn-ack Microsoft Terminal Services
| ssl-cert: Subject: commonName=Escape
| Issuer: commonName=Escape
|_ssl-date: 2024-06-24T12:37:45+00:00; +13s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: ESCAPE
|   NetBIOS_Domain_Name: ESCAPE
|   NetBIOS_Computer_Name: ESCAPE
|   DNS_Domain_Name: Escape
|   DNS_Computer_Name: Escape
|   Product_Version: 10.0.19041
|_  System_Time: 2024-06-24T12:37:40+00:00
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

# Foot Hold

```
xfreerdp /u:guest /v:<IP>

```

![image](https://github.com/reneanto/write-ups/assets/44943249/b53a7960-8ae1-44ac-ae3d-82236e4512d7)

![image](https://github.com/reneanto/write-ups/assets/44943249/f7f74ba5-529c-4209-8996-f5385230aea2)
