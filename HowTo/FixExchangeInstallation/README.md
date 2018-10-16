# An incomplete installation was detected. Run setup to complete Exchange installation

When you resume a removal of Exchange server, you are getting "An incomplete installation was detected. Run setup to complete Exchange installation" error message.

## Cause of setup interruption

It can be caused by antivirus software with system services protection enabled. During Exchange server decommission, setup is trying to stop Exchange services but AV blocks this activity, causing setup to fail.

## Resolution

There is no need to repair the Exchange server. Make sure that antivirus software is stopped, then in command line run the following command:

```text
setup /Mode:Uninstall /IAcceptExchangeServerLicenseTerms
```

Once setup is complete, you can reboot your Exchange server. It will boot up as a regular member server.