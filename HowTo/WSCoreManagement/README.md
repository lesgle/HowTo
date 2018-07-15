# Windows Server Core - Managing Programs, Updates and Drivers

Get information about installed software using registry

```text
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall
```

WMI method

```text
wmic product
```

Managing Windows Updates

```text
wmic qfe
Wusa /uninstall /kb:XXXXXXX
```

Managing Drivers

```text
Sc query type= driver
pnputil -d -f package.inf
```