# Mobile - Testing

## ADB

* Recently I've faced an issue while trying to connect to a mobile device via ADB
* when I listed down the devices via `adb devices` , it showed me that the device is unauthorized
* So I navigated to Developer Settings in the android and clicked on `Revoke USB debugging authorization`
* Then restarted the adb server via

```bash
adb kill-server
adb start-server
```

* Then allowed the device to reconnect which now is authorized