# 🔧 COM Hijacking

## <mark style="color:red;">COM</mark>

> The Microsoft Component Object Model (COM) is an interface standard that allows the software components to interact and communicate with each other’s code without knowledge of their internal implementation.

#### In simple words, COM is a system that helps developers build libraries which can co-operate with other apps developed with other languages.

OM objects are identified by their globally unique identifiers (GUIDs) known as class identifiers (CLSIDs) and interface identifiers (IIDs) and they are registered in registry hives.&#x20;

The merged registry hive HKCR contains the combined information of HKCU and HKLM.

```
HKEY_CURRENT_USER\Software\Classes\<CLSID>  (Per-User)

HKEY_LOCAL_MACHINE\Software\Classes\<CLSID> (System-Wide)
```

{% hint style="info" %}
COM usually lives in a DLL, sometimes as a EXE.&#x20;

It can also live on a remote machine.
{% endhint %}

When COM is present as a DLL, the process of COM loading looks like this:

![](<../../../.gitbook/assets/image (34).png>)

When COM is a EXE file the process will look like this:

![](<../../../.gitbook/assets/image (29).png>)

and if the COM object is on a remote machine:

![](<../../../.gitbook/assets/image (22).png>)

## <mark style="color:red;">COM Hijacking</mark>

One way to hijack the COM is by modifying the registry key in `HKEY_CUTRRENT_USER.`

![](<../../../.gitbook/assets/image (47).png>)

to find hijackable COM objects we can look in scheduled tasks:

```
schtasks /query /xml > c:\RTO\PERS\01.USER\tasks.xml
```

look for `<ComHandler>` in the output file, this will point to a specific class ID.&#x20;
