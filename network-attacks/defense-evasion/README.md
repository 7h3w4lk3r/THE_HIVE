# 🔴 Defense Evasion

## <mark style="color:red;">OS Security Controls & Solutions</mark>

#### <mark style="color:green;">Windows :</mark>

* Anti-Virus (AV)
* End-Point Detection and Response (EDR)
* Application whitelisting (AW) , AppLocker, SRP
* Anti-Malware Scanning Interface (AMSI)
* User Access Control (UAC)
* Windows Defender Application Control (WDAC)
* Windows Defender Application Guard (WDAG)
* Exploit Guard
* Sandboxes
* Just Enough Admin (JEA)
* Just in Time Admin (JITA)
* Privilege Access Workstations (PAW)
* Privilege Access Management (PAM)
* Credential Guard (CG) & Remote Credential Guard (RCG)
* Local Administration Password Solution (LAPS)
* Resource Based Constrained Delegation (RBCD)

#### <mark style="color:green;">Linux:</mark>

* Application Restriction (AppArmor, SELinux)

## <mark style="color:red;">EDR vs AV</mark>

![](<../../.gitbook/assets/image (12).png>)

## <mark style="color:red;">EDR Technologies</mark>

![](<../../.gitbook/assets/image (51).png>)

### <mark style="color:orange;">Process Creation</mark>

* EDRs look at parent child relationships to detect potential malicious activity, for example, should Word be spawning PowerShell? EDRs look at these relationships and find malicious activity based on this. They also may look at arguments, environment variables etc.
* This is exposed in the kernel via image loads and callbacks
* EDRs may subscribe to these events to get insight into this
* These callbacks and image loads include

```
PsSetCreateProcessNotifyRoutine()
PsSetCreatethreadNotifyRoutine()
PsSetLoadImageNotifyRoutine()
```

![](<../../.gitbook/assets/image (7) (1).png>)

### <mark style="color:orange;">AMSI</mark>

* This is used to detect .NET malware like C# PowerShell, VBS, jscript etc.
* In these processes, there is going to be a language provider loaded into memory which converts the raw source code of the script to machine instructions for the process itself
* these providers send potentially dangerous code into the amsi.dll loaded in the process, amsi.dll will then send this data to providers, which decide if the code is malicious or not
* The default provider in windows is MsMpEng.exe, but EDRs have been implementing their own providers and such(via some COM RPC magic shit). They communicate via RPC

![](<../../.gitbook/assets/image (31) (1) (1).png>)

### <mark style="color:orange;">Event Logging</mark>

* Events that get reported to svchost.exe
* Let's take powershell.exe as an example
* Powershell.exe has a System.Management.Automation.dll which is the powershell .NET implementation
* When certain events in System.Management.Automation.dll
* happen, it will get reported with EventWriteTransfer(), which will get traced down to EtwEventWrite(), and then into NtTraceEvent() which is a kernel syscall
* Then, the kernel has the event information which it then sends to Eventlog Service which then spits out the logs back into the user

![](<../../.gitbook/assets/image (18) (1) (1).png>)

### <mark style="color:orange;">Userland Hooking</mark>

* Injects a dll which hooks certain "potentially malicious functions" to see their behavior and deduce if they are malicious or not
* For example, we can hook NtWriteVirtualMemory and scan the buffer passed to it at runtime

![](<../../.gitbook/assets/image (42) (1) (1).png>)

### <mark style="color:orange;">Process Memory Scanning</mark>

* scanning process memory for malicious signatures.
* The scans can be triggered on specific events for example:
  * RWX regions
  * After a certain time period
  * After a process is created
  * etc.

{% hint style="info" %}
Note that this is very resource intensive, and periodic scanners can't do full memory scans in short intervals
{% endhint %}

### <mark style="color:orange;">EtwTi</mark>

* A way to have kernel level telemetry into common process injection APIs
* EtwTi is a kernel level instrumentation which does this
* APIs will trace down to the kernel, and go into a EtwTi function which goes to an ETW provider called Microsoft-Windows-Threat-Intelligence which then deduces if it is malicious or not
* EtwTi only available to processes running with PPL-Antimalware

{% embed url="https://blog.redbluepurple.io/windows-security-research/kernel-tracing-injection-detection" %}

### <mark style="color:orange;">Misc</mark>

* EDRs can dump process memory, grab files, and in the worst case, isolate a host from a network for detection and forensics.
* Least frequency analysis, EDRs find the odd one out and investigate to see if its malware. For example, all users run edge, but one runs chrome, and the chrome instance spawns a unknown binary from temp, chances are is that this is malware(shit example).

EDRs can integrate with VirusTotal, and have some detection logic from there
