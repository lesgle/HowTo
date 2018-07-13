# Installing MSE on Windows Server 2012 R2

Here is one unsupported scenario for **testing** purposes only of installing Microsoft Security Essentials on Windows Server 2012 R2.

If you try to run `mseinstall.exe` you will get an error message: Microsoft Security Essentials cannot be installed on your operating system. Your version of the Windows Operating system is not supported by this program.

![screen1](screen1.png)

Right click on `mseinstall.exe` file and Unblock it on General tab.

![screen2](screen2.png)

Set Compatibility mode to Windows 7. Make sure you set "Change settings for all users" if you edit it by non-admin user.

![screen3](screen3.png)

Start command line as Administrator and run the installer

```cmd
mseinstall.exe /disableoslimit
```