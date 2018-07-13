# Exchange Online – Blocked (unlicensed) user appears in GAL

## Issue

By default, when you unassign a license from Office 365 account using portal (Azure AD Connect is in place), it removes the Exchange Online mailbox. Thus, an account is removed from Global Address List (GAL). However, in some situation mailbox still appears in GAL. It happens when retention is assigned to the mailbox.

## Resolution

Extend an Active Directory Schema by the latest Exchange Cumulative Update. No need to install Exchange on premises, simply run the command below from Exchange installation folder.

```cmd
setup.exe /PrepareSchema /IAcceptExchangeServerLicenseTerms
```

Then start **Azure AD Connect** and Refresh directory schema. It will enable a synchronization of new Exchange attributes to the cloud. Then populate **MailNickname** attribute.

Final step will be setting up **msExchangeHiddenFromAddressList** attribute.