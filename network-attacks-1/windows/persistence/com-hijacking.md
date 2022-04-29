# COM Hijacking

## <mark style="color:red;">COM</mark>

COM is a platform independent, distributed, object-oriented system for creating binary software components. as microsoft says:

> The Microsoft Component Object Model (COM) is an interface standard that allows the software components to interact and communicate with each other’s code without knowledge of their internal implementation.

#### COM is a technology that allows objects to interact across process and computer boundaries as easily as within a single process.

It uses client/server architecture. COM clients are the programs that use COM objects, and the COM servers are the COM objects themselves. The COM server can be hosted either in a DLL (called an in-process server) or in an EXE (called an out-of-process server)

COM objects ,can be created and used by in any languages, was designed to support reusable software components that could be utilized by all programs. Some COM clients examples:

* VBA -> CreateObject
* PowerShell -> New-Object -COMObject
* Python -> pywin32
* Ruby -> win32ole

OM objects are identified by their globally unique identifiers (GUIDs) known as class identifiers (CLSIDs) and interface identifiers (IIDs) and they are registered in registry hives.&#x20;

The merged registry hive HKCR contains the combined information of HKCU and HKLM.

```
HKEY_CURRENT_USER\Software\Classes\<CLSID>  (Per-User)

HKEY_LOCAL_MACHINE\Software\Classes\<CLSID> (System-Wide)
```

{% hint style="info" %}
COM usually lives in a DLL, sometimes as a EXE.&#x20;

It can also reside in a remote machine.
{% endhint %}
