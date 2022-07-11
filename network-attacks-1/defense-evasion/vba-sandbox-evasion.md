# 🔴 VBA Sandbox Evasion

{% embed url="https://github.com/joesecurity/pafishmacro" %}

Check for analysis tools with WMI:

```vba
Public Sub checkApps()

    printMsg "[*] WordBasic.AppGetNames ..."
    
    d = False
    tns = Array("vmware", "vmtools", "vbox", "process explorer", "processhacker", "procmon", "visual basic", "fiddler", "wireshark")
    Set ws = GetObject("winmgmts:\\.\root\cimv2")
    
    Dim names() As String
    ReDim names(WordBasic.AppCount())
    
    WordBasic.AppGetNames names
    
    For Each n In names
        For Each tn In tns
            If InStr(LCase(n), tn) > 0 Then
                d = True
            End If
        Next
    Next

    If d Then
    
        printMsg "DETECTED"
        
    Else
    
        printMsg "OK"
    End If
    
End Sub
```

Check the number of processes:

```vba
Public Sub checkAppCount()

    printMsg "[*] Checking WordBasic.AppCount() ..."

    If WordBasic.AppCount() < 50 Then
    
        printMsg "DETECTED"
        
    Else
    
        printMsg "OK"
    End If
    
End Sub
```

Check for file name change:

```vba
Public Sub checkPreciseFileName()

    printMsg "[*] Checking Precise Filename ..."
    
    badName = False

  
    If ActiveDocument.Name <> "Pafish.docm" Then
            badName = True
    End If
 
    If badName Then
        
        printMsg "DETECTED"
    Else
        
        printMsg "OK"
    End If
    
End Sub
```

```vba
Public Sub checkFilenameBad()

    printMsg "[*] Checking Bad Filename ..."
    
    badName = False
    badNames = Array("malware", "myapp", "sample", ".bin", "mlwr_", "Desktop")

    
    For Each n In badNames
        If InStr(LCase(ActiveDocument.FullName), n) > 0 Then
            badName = True
        End If
    Next
 

    If badName Then
        
        printMsg "DETECTED"
    Else
        
        printMsg "OK"
    End If
    
End Sub
```

Check for analysis tasks:

```vba
Public Sub checkTasks()

    printMsg "[*] Checking Application.Tasks.Name ..."

    badTask = False
    badTaskNames = Array("vbox", "vmware", "vxstream", "autoit", "vmtools", "tcpview", "wireshark", "process explorer", "visual basic", "fiddler")
    
    For Each Task In Application.Tasks
    
        For Each badTaskName In badTaskNames
            If InStr(LCase(Task.Name), badTaskName) > 0 Then
                badTask = True
            End If
        Next
        
    Next

    If badTask Then
        
        printMsg "DETECTED"
    Else
        
        printMsg "OK"
    End If
    
End Su
```

Check the number of cores (less than 3):

```vba
Public Sub checkCores()

    printMsg "[*] Checking Win32_Processor.NumberOfCores ..."

    badCores = 0

    Set objWMIService = GetObject("winmgmts:\\.\root\cimv2")
    Set colItems = objWMIService.ExecQuery("Select * from Win32_Processor", , 48)
    
    For Each objItem In colItems
    
            If objItem.NumberOfCores < 3 Then
                badCores = True
            End If
        
    Next

    If badCores Then
        
        printMsg "DETECTED"
    Else
        
        printMsg "OK"
    End If
    
End Sub
```

Check BIOS for VM names:

```vba
Public Sub checkBios()

    printMsg "[*] Checking Win32_Bios.SMBIOSBIOSVersion & SerialNumber ..."

    badBios = False
    badBiosNames = Array("virtualbox", "vmware", "kvm")
    
    Set objWMIService = GetObject("winmgmts:\\.\root\cimv2")
    Set colItems = objWMIService.ExecQuery("Select * from Win32_Bios", , 48)
    
    For Each objItem In colItems
    
        For Each badName In badBiosNames
            If InStr(LCase(objItem.SMBIOSBIOSVersion), badName) > 0 Then
                badBios = True
            End If
            If InStr(LCase(objItem.SerialNumber), badName) > 0 Then
                badBios = True
            End If
        Next
        
    Next

    If badBios Then
        
        printMsg "DETECTED"
    Else
        
        printMsg "OK"
    End If
    
End Sub
```

Check username:

```vba
Public Sub checkUsername()

    printMsg "[*] Checking Win32_ComputerSystem.Username ..."

    badUsername = False
    badUsernames = Array("admin", "malfind", "sandbox", "test")
    
    Set objWMIService = GetObject("winmgmts:\\.\root\cimv2")
    Set colItems = objWMIService.ExecQuery("Select * from Win32_ComputerSystem", , 48)
    
    For Each objItem In colItems
    
        For Each badName In badUsernames
            If InStr(LCase(objItem.UserName), badName) > 0 Then
                badUsername = True
            End If
        Next
        
    Next

    If badUsername Then
        
        printMsg "DETECTED"
    Else
        
        printMsg "OK"
    End If
    
End Sub
```

Check the number of recent documents opened:

```vba
Public Sub checkRecentDocs()

    printMsg "[*] Checking Application.RecentFiles.Count ..."

    If Application.RecentFiles.Count < 3 Then
    
        printMsg "DETECTED"
        
    Else
    
        printMsg "OK"
    End If
    
End Sub
```
