# Exchange Server - Change default mailbox calendar

Create new calendar using Outlook (i.e. Calendar2)

Run following cmdlet to see the difference

```powershell
Get-MailboxFolderStatistics mailbox@example.com | where {$_.identity -like "*\Calendar*"} | fl Identity,FolderType
```

```test
Identity   : mailbox@example.com\Calendar
FolderType : Calendar
Identity   : mailbox@example.com\Calendar2
FolderType : User Created
```

Rename Calendar2 to Calendar using https://mfcmapi.codeplex.com/

Run "outlook.exe /resetfolders"

Run cmdlet from 2 step to make sure your renamed mailbox got Calendar folder type

It can be used in scenario when calendar is "somehow" moved to deleted items and you can't move it back due to following error: "Cannot move the items. Special and default folders, including Inbox, Contacts, Calendar, Task, and Solution folders, cannot be moved."