# Logon Scripts

#### Windows allows logon scripts to be run whenever a specific user or group of users log into a system. The Scripts can be used to perform administrative functions, which may often execute other programs or send information to an internal logging server.

#### &#x20;If adversaries can access these scripts, they may insert additional code into the logon script to execute their tools when a user logs in.&#x20;

This code can allow them to maintain persistence on a single system, if it is a local script, or to move laterally within a network, if the script is stored on a central server and pushed to many systems. Depending on the access configuration of the logon scripts, either local credentials or an administrator account may be necessary.

```
reg add "HKEY_CURRENT_USER\Environment" /v UserInitMprLogonScript /d "c:\RTO\PERS\01.USER\logon.bat" /t REG_SZ /f
```

## <mark style="color:red;">Detection</mark>

* Monitor executed commands and arguments for logon scripts
* Monitor for newly constructed processes and/or command-lines that execute logon scripts
* Monitor for the creation/modification to Registry keys associated with Windows logon scrips, nameley `HKCU\Environment\UserInitMprLogonScript`.

use AutoRuns.exe to find logon tasks.
