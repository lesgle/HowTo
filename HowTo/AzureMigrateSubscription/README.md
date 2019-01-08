# How to migrate Azure VM to another account

Here is the way how to easily migrate Azure VM to another account. As VM uses unmanaged disks, some PowerShell workaround is required.

## Basic steps

* Migrate VHD file to another account using Microsoft Azure Storage Explorer  
* Recreate VM on destination account and replace OS disk using PowerShell

## Swap Azure VM OS disk

Make sure that VM is stopped.  
Find out a destination VHD url.  
Then run the following PowerShell commands:

```PowerShell
$VM = Get-AzureRmVM -Name YourVMName -ResourceGroupName ResourceGroupName
$VM.StorageProfile.OsDisk.Vhd.Uri = "VHD_URL"
Update-AzureRmVM -VM $VM -ResourceGroupName ResourceGroupName
```

Then start your VM and it will boot up from new OS disk.