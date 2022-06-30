# 🔧 SRP & AppLocker Bypass

## <mark style="color:red;">Check AppLocker Policies</mark>

```
Get-AppLockerPolicy -Local).RuleCollections
Get-ChildItem -Path HKLM:Software\Policies\Microsoft\Windows\SrpV2 -Recurse
reg query HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\SrpV2\Exe\
```

## <mark style="color:red;">Applocker: Writable Windows Directories</mark>

```
# list from https://github.com/api0cradle/UltimateAppLockerByPassList/blob/master/Generic-AppLockerbypasses.md
C:\Windows\Tasks
C:\Windows\Temp
C:\windows\tracing
C:\Windows\Registration\CRMLog
C:\Windows\System32\FxsTmp
C:\Windows\System32\com\dmp
C:\Windows\System32\Microsoft\Crypto\RSA\MachineKeys
C:\Windows\System32\spool\PRINTERS
C:\Windows\System32\spool\SERVERS
C:\Windows\System32\spool\drivers\color
C:\Windows\System32\Tasks\Microsoft\Windows\SyncCenter
C:\Windows\System32\Tasks_Migrated (after peforming a version upgrade of Windows 10)
C:\Windows\SysWOW64\FxsTmp
C:\Windows\SysWOW64\com\dmp
C:\Windows\SysWOW64\Tasks\Microsoft\Windows\SyncCenter
C:\Windows\SysWOW64\Tasks\Microsoft\Windows\PLA\System
```

## <mark style="color:red;">Find Writable Files/Folders in Windows</mark>

```
$a = Get-ChildItem "c:\windows\" -recurse -ErrorAction SilentlyContinue
$a | % {
    $fileName = $_.fullname
    $acls = get-acl $fileName  -ErrorAction SilentlyContinue | select -exp access | ? {$_.filesystemrights -match "full|modify|write" -and $_.identityreference -match "authenticated users|everyone|$env:username"}
    if($acls -ne $null)
    {
        [pscustomobject]@{
            filename = $fileName
            user = $acls | select -exp identityreference
        }
    }
}
```

## <mark style="color:red;">Resources</mark>

{% embed url="https://www.infosecmatter.com/19-ways-to-bypass-software-restrictions-and-spawn-a-shell" %}

{% embed url="https://www.pentestpartners.com/security-blog/breaking-out-of-citrix-and-other-restricted-desktop-environments" %}

{% embed url="https://github.com/api0cradle/UltimateAppLockerByPassList" %}

{% embed url="https://www.wilderssecurity.com/threads/srp-easily-bypassed.250445" %}

{% embed url="https://agrrrdog.blogspot.com/2015/08/universal-way-to-bypass-group-policy.html" %}

{% embed url="https://infosecaddicts.com/bypass-windows-applocker" %}

{% embed url="https://blankandtech.blogspot.com/2019/04/how-to-bypass-windows-applocker.html" %}

{% embed url="https://blog.pwn.al/security/applocker/bypass/custom/rules/windows/2018/09/13/applocker-custom-rules-bypass.html" %}

{% embed url="https://pentestlab.blog/2017/05/24/applocker-bypass-control-panel" %}

{% embed url="https://github.com/api0cradle/UltimateAppLockerByPassList/blob/master/Generic-AppLockerbypasses.md" %}

{% embed url="https://www.bleepingcomputer.com/news/security/you-can-bypass-windows-applocker-protection-via-rogue-control-panel-items" %}

{% embed url="https://pentestlab.blog/2018/05/10/applocker-bypass-cmstp" %}
