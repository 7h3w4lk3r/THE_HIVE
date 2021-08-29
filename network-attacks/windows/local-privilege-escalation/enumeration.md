# Enumeration

## System Information

### general system information

```text
systeminfo
```

{% hint style="info" %}
one way to find out whether you are in a virtual environment or not is to check the output. for example the picture bellow is the output from a windows 10 machine in virtualbox: 
{% endhint %}

![](../../../.gitbook/assets/image%20%2867%29.png)

for OS name and version \( can add any other filter too \) :

```text
systeminfo | findstr /B /C:"OS Name" /C:"OS Version"
```

show information for a remote system \( used in domains \) :

```text
systeminfo /S [ip or hostname]
```

environment variables:

```text
set
Get-ChildItem Env: | ft Key,Value
```

### 

### get a list of installed hot fixes

```text
Get-HotFix
```

patchs and updates:

```text
wmic qfe
```

list all drivers:

```text
wmic logicaldisk get caption || fsutil fsinfo drives
wmic logicaldisk get caption,description,providername
Get-PSDrive | where {$_.Provider -like "Microsoft.PowerShell.Core\FileSystem"}| ft Name,Root
```

### 

### Kernel Exploits

Extract the output of the systeminfo command:

```text
systeminfo > systeminfo.txt
```

Run [wesng](https://github.com/bitsadmin/wesng) to find potential exploits:

```text
python wes.py systeminfo.txt -i 'Elevation of Privilege' --exploits-only | less
```

Cross-reference results with compiled exploits:

{% embed url="https://github.com/SecWiki/windows-kernel-exploits" %}

{% embed url="https://github.com/nomi-sec/PoC-in-GitHub" %}

​[https://github.com/abatchy17/WindowsExploit](https://github.com/abatchy17/WindowsExploits)





### SNMP configurations

check to see if there is any SNMP services running and find valid community strings:

```text
reg query HKLM\SYSTEM\CurrentControlSet\Services\SNMP /s 
Get-ChildItem -path HKLM:\SYSTEM\CurrentControlSet\Services\SNMP -Recurse
```

{% hint style="info" %}
for more information on windows registry hive and 'reg' commands refer to[ this section](%20https://7h3w4lk3r.gitbook.io/the-hive/network-attacks/windows/security-component/windows-registry) of the book.
{% endhint %}

### check  powershell version

```text
REG QUERY "HKLM\SOFTWARE\Microsoft\PowerShell\1\PowerShellEngine" /v PowerShellVersion
```

### navigate the registry with powershell

might require admin rights or higher privileges

```text
PS C:\> cd HKLM:\
PS HKLM:\> ls
```

### get last system boot time

```text
$os = Get-WmiObject win32_operatingsystem $uptime = (Get-Date) - $os.ConvertToDateTime($os.LastBootUpTime) Write-Output ("Last boot: " + $os.ConvertToDateTime($os.LastBootUpTime))
You can also run this single line to get last boot time
systeminfo | more
```

### powershell history <a id="powershell-history"></a>

```text
ConsoleHost_history #Find the PATH where is saved
​
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
type C:\Users\swissky\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
type $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
cat (Get-PSReadlineOption).HistorySavePath
cat (Get-PSReadlineOption).HistorySavePath | sls passw
```

### network and connections

```text
netstat -ano
ipconfig /all
arp -a
reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
reg query "HKLM\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
```

routing info:

```text
route print
Get-NetRoute -AddressFamily IPv4 | ft DestinationPrefix,NextHop,RouteMetric,ifIndex
```

network shares

```text
net share
Find-DomainShare -ComputerDomain domain.local
```





### system logs and audit setting

view system auditing setting

```text
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System\Audit
```

check if system logs are stored somewhere else:

```text
reg query HKLM\Software\Policies\Microsoft\Windows\EventLog\EventForwarding\SubscriptionManager
```

view log categories:

```text
wevtutil enum-logs
wevtutil el
```

display configuration information about the System log on the local computer:

```text
wevtutil gl System
```

general application logging info:

```text
wevtutil gli Application
```

check credential guard:

```text
reg query HKLM\System\CurrentControlSet\Control\LSA /v LsaCfgFlags
```

check cached credentials \(domain\):

```text
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\WINDOWS NT\CURRENTVERSION\WINLOGON" /v CACHEDLOGONSCOUNT
```

### check for AVs

```text
WMIC /Node:localhost /Namespace:\\root\SecurityCenter2 Path AntiVirusProduct Get displayName /Format:List | more
Get-MpComputerStatus 
```

### applocker policy

```text
Get-ApplockerPolicy -Effective -xml
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
$a = Get-ApplockerPolicy -effective
$a.rulecollections
```

### check if UAC is enabled

```text
reg query HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ 
```

## Users and Groups

Get current username

```text
echo %USERNAME% || whoami
$env:username
```

list privileges

```text
whoami /priv
whoami /groups
```

list all local users and groups

```text
net user
whoami /all
net localgroup
Get-LocalUser | ft Name,Enabled,LastLogon
Get-ChildItem C:\Users -Force | select Name
Get-WmiObject -Class Win32_UserAccount
Get-LocalGroupMember Administrators | ft Name, PrincipalSource
```

find local admins

```text
net localgroup Administrators 
```

list logon requirements \(useful for bruteforcing\)

```text
net accounts
```

get details about a user

```text
net user [username]
```

get details about a group

```text
net localgroup administrators
Get-LocalGroupMember Administrators | ft Name, PrincipalSource
```

























