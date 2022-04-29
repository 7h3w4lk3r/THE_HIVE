# Service Creation/Modification

#### With Admin access we can create a new service to run our payload at start up or modify existing service and replace the binary/path to do the same.

## <mark style="color:red;">Create a New Service</mark>

```
sc create UpdateService binpath= c:\rto\pers\implant\implantsrv.exe start= auto
sc query UpdateService
sc start UpdateService
```

## <mark style="color:red;">Modify Existing service</mark>

change the binary path

```
sc config UpdateService binpath= "c:\windows\system32\notepad.exe"
sc stop UpdateService
sc start UpdateService
```

## <mark style="color:red;">Mitigation</mark>

* On Windows 10, enable Attack Surface Reduction (ASR) rules to block processes created by [PsExec](https://attack.mitre.org/software/S0029) from running.
* Ensure that permissions disallow services that run at a higher permissions level from being created or interacted with by a user with a lower permission level.

## <mark style="color:red;">Detection</mark>

* Check Startup services with AutoRuns.exe
* Monitor for changes made to windows registry keys and/or values that may abuse the Windows service control manager to execute malicious commands or payloads.
* Monitor system service creation.
