# Ubuntu 16.04 - Failed to start LXD

## Issue

```text
systemctl status lxd-containers.service
```

```text
● lxd-containers.service - LXD - container startup/shutdown
   Loaded: loaded (/lib/systemd/system/lxd-containers.service; enabled; vendor preset: enabled)
   Active: failed (Result: exit-code) since Tue 2017-03-21 13:33:45 EDT; 3min 15s ago
     Docs: man:lxd(1)
 Main PID: 852 (code=exited, status=1/FAILURE)

Mar 21 13:33:45 hostname systemd[1]: Starting LXD - container startup/shutdown...
Mar 21 13:33:45 hostname lxd[852]: error: open /var/lib/lxd/containers: no such file or directory
Mar 21 13:33:45 hostname systemd[1]: lxd-containers.service: Main process exited, code=exited, status=1/FAILURE
Mar 21 13:33:45 hostname systemd[1]: Failed to start LXD - container startup/shutdown.
Mar 21 13:33:45 hostname systemd[1]: lxd-containers.service: Unit entered failed state.
Mar 21 13:33:45 hostname systemd[1]: lxd-containers.service: Failed with result 'exit-code'.
```

## Fix

```text
sudo su
service lxd restart
reboot
```

```text
systemctl status lxd-containers.service
```

```text
● lxd-containers.service - LXD - container startup/shutdown
   Loaded: loaded (/lib/systemd/system/lxd-containers.service; enabled; vendor preset: enabled)
   Active: active (exited) since Tue 2017-03-21 13:37:14 EDT; 2min 4s ago
     Docs: man:lxd(1)
 Main PID: 857 (code=exited, status=0/SUCCESS)
   CGroup: /system.slice/lxd-containers.service

Mar 21 13:37:13 hostname systemd[1]: Starting LXD - container startup/shutdown...
Mar 21 13:37:14 hostname systemd[1]: Started LXD - container startup/shutdown.
```