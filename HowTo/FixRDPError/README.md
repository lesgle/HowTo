# RDP fails with 36870 error

## Issue

One of your Azure VM's (Windows Server 2012 R2) stops connecting via RDP with the error below:

```text
A fatal error occurred when attempting to access the SSL server credential private key. The error code returned from the cryptographic module is 0x8009030D. The internal error state is 10001.
```

Server is online but only RDP is unavailable.

## Resolution

Connect to VM using `psexec` tool.

```text
psexec \\servername cmd
```

Then on a remote session check a System log. The command below shows last 10 events from a System log:

```text
wevtutil qe System /c:10 /rd:true /f:text
```

You should see the errors with `36870` or `1057` ID's.

Check a server certificate. From management station (it should have a network access to the server, preferably from the same vNet) open `mmc` and add `Certificates` snap-in. Select `Computer Account` then `Another Computer` and type server IP or DNS name. Check the certificate under `Remote Desktop` group.

If the certificate is expired, remove it and restart the RDP service by running:

```text
net stop sessionenv
net start sessionenv
```

Try to connect. If it still fails, try to generate a new self-service certificate and assign it to the RDP service.

```text
wmic /namespace:\\root\cimv2\TerminalServices PATH Win32_TSGeneralSetting Set SSLCertificateSHA1Hash="Certificate_Thumbprint"
```

## References

https://social.technet.microsoft.com/wiki/contents/articles/36151.renew-the-rdp-self-signed-certificate.aspx  
https://support.microsoft.com/en-us/help/3042780/remote-desktop-listener-certificate-configurations-in-windows-server-2