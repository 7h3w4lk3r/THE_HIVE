# Managed Security Groups

​[https://stealthbits.com/blog/exploiting-weak-active-directory-permissions-with-powersploit](https://stealthbits.com/blog/exploiting-weak-active-directory-permissions-with-powersploit/)

​Returns all security groups in the current \(or target\) domain that have a manager set:

```text
PowerView3 > Get-DomainManagedSecurityGroup
​
GroupName                : Security Operations
GroupDistinguishedName   : CN=Security Operations,CN=Users,DC=MEGACORP,DC=LOCAL
ManagerName              : john.doe
ManagerDistinguishedName : CN=John Doe,OU=Security,OU=IT,OU=Employees,DC=MEGACORP,DC=LOCAL
ManagerType              : User
ManagerCanWrite          : UNKNOWN
```

Enumerate the ACLs set on this group. `GenericWrite` privilege means that the user can modify group membership:

```text
PowerView3 > $sid = ConvertTo-SID john.doe
PowerView3 > Get-DomainObjectAcl -Identity 'Security Operations' | ? {$_.SecurityIdentifier -eq $sid}
​
ObjectDN              : CN=Security Operations,CN=Users,DC=MEGACORP,DC=LOCAL
ObjectSID             : S-1-5-21-3167813660-1240564177-918740779-2549
ActiveDirectoryRights : ListChildren, ReadProperty, GenericWrite
BinaryLength          : 36
AceQualifier          : AccessAllowed
IsCallback            : False
OpaqueLength          : 0
AccessMask            : 131132
SecurityIdentifier    : S-1-5-21-3167813660-1240564177-918740779-1874
AceType               : AccessAllowed
AceFlags              : ContainerInherit
IsInherited           : False
InheritanceFlags      : ContainerInherit
PropagationFlags      : None
AuditFlags            : None
```

