# Service Creation

With Admin access we can create a new service to run our payload at start up or modify existing service and replace the binary/path to do the same.

```
C:\> sc create evilsvc binpath= "c:\payload.exe" start= "auto" obj= "LocalSystem" password= ""
```

## <mark style="color:red;">Mitigation</mark>

* On Windows 10, enable Attack Surface Reduction (ASR) rules to block processes created by [PsExec](https://attack.mitre.org/software/S0029) from running.
* Ensure that permissions disallow services that run at a higher permissions level from being created or interacted with by a user with a lower permission level.

## <mark style="color:red;">Detection</mark>

* Check Startup services with AutoRuns.exe
* Monitor for changes made to windows registry keys and/or values that may abuse the Windows service control manager to execute malicious commands or payloads.
* Monitor system service creation.
