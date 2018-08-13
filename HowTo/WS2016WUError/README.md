# Windows Server 2016 - windows updates error

## Issue

When you install windows updates on Windows Server 2016 you get: `Error 0x80070005`

In update log you see: `HRESULT = 0x80070005 - E_ACCESSDENIED`

## Resolution

Stop the `Automatic Updates` service.

Create `\Windows\SoftwareDistributionNew` folder somewhere beyond C drive (D drive for example).

Move all files and folders in `C:\Windows\SoftwareDistribution` to folder created above.

Delete the `C:\Windows\SoftwareDistribution` folder.

Create soft link:

```text
mkLink /D C:\Windows\SoftwareDistribution D:\Windows\SoftwareDistributionNew
```

Start the `Automatic Updates` service.

Run Windows Updates again. If updates work, remove soft link and run updates again.