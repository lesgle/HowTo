# Fix Windows Updates 0x8024402F and 0x8024000E errors

## Errors

```text
Log Name:      Microsoft-Windows-WindowsUpdateClient/Operational
Source:        Microsoft-Windows-WindowsUpdateClient
Date:          17/02/2019 6:00:00
Event ID:      25
Task Category: Windows Update Agent
Level:         Error
Keywords:      Failure,Check for Updates
User:          SYSTEM
Computer:      COMPUTER.example.com
Description:
Windows Update failed to check for updates with error 0x8024402F.
```

Additionally Windows Logs can produce the following error as well:

```text
Windows Update failed to check for updates with error 0x8024000E.
```

## Affected Systems

Windows Server 2016 Datacenter in Microsoft Azure with 2GB of RAM.

## Resolution

Increase RAM and start Windows Updates installation. Then you can set RAM back to its original size.

## Additional Notes

"sfc /scannow" may show the system corruption files, including "Overlap Duplicate Ownership". It can be ignored.

Despite errors from "sfc" scan, DISM tool shows the system as healthy:

```text
DISM.exe /Online /Cleanup-image /scanhealth
```

These commands can be used if above reports the errors:

  ```text
DISM.exe /Online /Cleanup-image /Restorehealth 
DISM.exe /Online /Cleanup-Image /RestoreHealth /Source:C:\RepairSource\Windows /LimitAccess
```

This procedure can be used for resetting Windows Updates components manually. Windows Server 2016 has a very good build-in troubleshooter, which does almost the same automatically:

```text
net stop wuauserv
net stop cryptSvc
net stop bits
net stop msiserver
ren C:\Windows\SoftwareDistribution SoftwareDistribution.old
ren C:\Windows\System32\catroot2 Catroot2.old
net start wuauserv
net start cryptSvc
net start bits
net start msiserver
```

Cleaning CBS update pending records:

* Delete "PackagesPending" and "RebootPending" in "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing".
* Delete "pending.xml" in "C:\Windows\WinSxS"

Delete temporary files:

```text
C:\windows\temp\
C:\Users\%Username%\AppData\Local\Temp\
C:\Windows\Installer\*.tmp
C:\Windows\Panther\*.tmp
```