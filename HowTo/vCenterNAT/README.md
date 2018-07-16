# ESXi - vCenter behind NAT

vi /etc/vmware/vpxa/vpxa.cfg

```text
<config>
<vpxa>
...
<serverip>Public vCenter IP Address</serverip>
<preserveserverip>true</preserveserverip>
</vpxa>
...
</config>
```

On gateway which holds Public vCenter IP Address add following NAT rules:

```
Internal vCenter IP - TCP 5989
Internal vCenter IP - UDP 902
```

In vCenter - Administration - vCenter Server Settings - Runtime Settings - Managed IP Address. Enter Public vCenter IP.

On ESXi via ssh

```text
services.sh restart
```