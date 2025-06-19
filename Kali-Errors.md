
# Kali Login Errors

 Ever booted into tty with something unknown try the following.

## Read only File system

* check for file system type by

``` bash
sudo fsck -Af -M
```


* Just remount the file system like

```bash
sudo mount -o remount,rw /
```


## Login to GUI

* Since Lightdm is the default gui manager for Kali, check it's status by running
  
``` bash
  sudo systemctl status ligtdm
```
 
*  If it is down then enable it and start it by
  
```bash
sudo systemctl enable ligtdm && sudo systemctl start lightdm
```

## Temporary DNS

* Dns errors while update, do this

```bash
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf > /dev/null
```