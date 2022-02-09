# ACL Abuse

## ACL BloodHound Abuse Hierarchy

![](<../../../../.gitbook/assets/image (271).png>)

Some AD object security permissions abusable with PowerView/SharpView:

* **ForceChangePassword** abused with `Set-DomainUserPassword`.
* **AddMembers** abused with `Add-DomainGroupMember`.
* **GenericAll** abused with `Set-DomainUserPassword` or `Add-DomainGroupMember`.
* **GenericWrite** abused with `Set-DomainObject`.
* **WriteOwner** abused with `Set-DomainObjectOwner`.
* **WriteDACL** abused with `Add-DomainObjectACL`.
* **AllExtendedRights** abused with `Set-DomainUserPassword` or `Add-DomainGroupMember`.

## SDDL (Security Descriptor Definition Language)

At the lowest level, the Security Descriptor Definition Language is used in the nTSecurityDescriptor attribute (and on registry keys and NTFS files) to define the ACL. Fortunately, one does not need to know this level of detail in normal conditions.

But advanced administrators may want to write scripts or code that can correctly construct SDDL strings. Also the security templates (located at %systemroot%\security\templates) use SDDL if you manually edit them with a text editor instead of the MMC interface.manually editing these templates turns out to be the most effective way to manipulate them. It is most likely that you will simply need to be able to read a SDDL string.

### Format of nTSecurityDescriptor string

```
O:owner_sidG:group_sidD:dacl_flags(string_ace1)(string_ace2)…
(string_acen)S:sacl_flags(string_ace1)(string_ace2)…(string_acen)
```

Each nTSecurityDescriptor SDDL string is composed of 5 primary parts which correspond to the Header, DACL (D:), SACL (S:), primary group (G:)and owner (O:). Each of these parts is designated with the prefix noted in parenthesis. The header contains some record keeping information, along with 2 flags that designate whether the object is blocking inheritance for the SACL and DACL. The contents of both the primary group and owner parts are simply a single SID. The contents of both the SACL and DACL parts are a string with no fixed length. ACEs make up the contents of these strings. ACEs are enclosed within parenthesis, and there are 6 fields in each ACE. These 6 fields are separated by a semicolon delimiter. The fields are ACE type (allow/deny/audit), ACE flags (inheritance and audit settings), Permissions (list of incremental permissions), ObjectType (GUID), Inherited Object Type (GUID), and Trustee (SID).

### ACE Type

The ACE type designates whether the trustee is allowed, denied or audited.

| **Value** | **Description**                                                   |
| --------- | ----------------------------------------------------------------- |
| “A”       | ACCESS ALLOWED                                                    |
| “D”       | ACCESS DENIED                                                     |
| “OA”      | OBJECT ACCESS ALLOWED: ONLY APPLIES TO A SUBSET OF THE OBJECT(S). |
| “OD”      | OBJECT ACCESS DENIED: ONLY APPLIES TO A SUBSET OF THE OBJECT(S).  |
| “AU”      | SYSTEM AUDIT                                                      |
| “AL”      | SYSTEM ALARM                                                      |
| “OU”      | OBJECT SYSTEM AUDIT                                               |
| “OL”      | OBJECT SYSTEM ALARM                                               |

### ACE Flags

The ACE flags denote the inheritance options for the ACE, and if it is a SACL, the audit settings.

| **Value** | **Description**                                                                                                |
| --------- | -------------------------------------------------------------------------------------------------------------- |
| “CI”      | CONTAINER INHERIT: Child objects that are containers, such as directories, inherit the ACE as an explicit ACE. |
| “OI”      | OBJECT INHERIT: Child objects that are not containers inherit the ACE as an explicit ACE.                      |
| “NP”      | NO PROPAGATE: ONLY IMMEDIATE CHILDREN INHERIT THIS ACE.                                                        |
| “IO”      | INHERITANCE ONLY: ACE DOESN’T APPLY TO THIS OBJECT, BUT MAY AFFECT CHILDREN VIA INHERITANCE.                   |
| “ID”      | ACE IS INHERITED                                                                                               |
| “SA”      | SUCCESSFUL ACCESS AUDIT                                                                                        |
| “FA”      | FAILED ACCESS AUDIT                                                                                            |

### Permissions

The Permissions are a list of the incremental permissions given (or denied/audited) to the trustee-these correspond to the permissions discussed earlier and are simply appended together. However, the incremental permissions are not the only permissions available. The table below lists all the permissions.

| **Value**                           | **Description**          | **Hexadecimal Value** | **Binary Bits from 0** |
| ----------------------------------- | ------------------------ | --------------------- | ---------------------- |
| **Generic access rights**           |                          |                       |                        |
| “GA”                                | GENERIC ALL              | 0x10000000            | Bit 28                 |
| “GR”                                | GENERIC READ             | 0x80000000            | Bit 31                 |
| “GW”                                | GENERIC WRITE            | 0x40000000            | Bit 30                 |
| “GX”                                | GENERIC EXECUTE          | 0x20000000            | Bit 29                 |
| **Directory service access rights** |                          |                       |                        |
| “RC”                                | Read Permissions         | 0x20000               | Bit 17                 |
| “SD”                                | Delete                   | 0x10000               | Bit 16                 |
| “WD”                                | Modify Permissions       | 0x40000               | Bit 18                 |
| “WO”                                | Modify Owner             | 0x80000               | Bit 19                 |
| “RP”                                | Read All Properties      | 0x00000010            | Bit 4                  |
| “WP”                                | Write All Properties     | 0x00000020            | Bit 5                  |
| “CC”                                | Create All Child Objects | 0x00000001            | Bit 0                  |
| “DC”                                | Delete All Child Objects | 0x00000002            | Bit 1                  |
| “LC”                                | List Contents            | 0x00000004            | Bit 2                  |
| “SW”                                | All Validated Writes     | 0x00000008            | Bit 3                  |
| “LO”                                | List Object              | 0x00000080            | Bit 7                  |
| “DT”                                | Delete Subtree           | 0x00000040            | Bit 6                  |
| “CR”                                | All Extended Rights      | 0x00000100            | Bit 8                  |
| **File access rights**              |                          |                       |                        |
| “FA”                                | FILE ALL ACCESS          |                       |                        |
| “FR”                                | FILE GENERIC READ        |                       |                        |
| “FW”                                | FILE GENERIC WRITE       |                       |                        |
| “FX”                                | FILE GENERIC EXECUTE     |                       |                        |
| **Registry key access rights**      |                          |                       |                        |
| “KA”                                | KEY ALL ACCESS           | 0xF003F               |                        |
| “KR”                                | KEY READ                 | 0x20019               |                        |
| “KW”                                | KEY WRITE                | 0x20006               |                        |
| “KX”                                | KEY EXECUTE              | 0x20019               |                        |
|                                     | KEY CREATE SUB KEYS      | 0x0004                |                        |
|                                     | KEY ENUMERATE SUB KEYS   | 0x0008                |                        |
|                                     | KEY NOTIFY               | 0x0010                |                        |
|                                     | KEY QUERY VALUE          | 0x0001                |                        |
|                                     | KEY SET VALUE            | 0x0002                |                        |

### Object Type and Inherited Object Type

The ObjectType is a GUID representing an object class, attribute, attribute set, or extended right. If present it limits the ACE to the object the GUID represents. The Inherited Object Type is a GUID representing an object class. If present it limits inheritance of the ACE to the child entries of only that object class.

### Trustee

The Trustee is the SID of the user or group being given access (or denied or audited). Instead of a SID, there are several commonly used acronyms for well-known SIDs. The most common are listed in the table below, but you can review more at [https://docs.microsoft.com/en-us/windows/win32/secauthz/sid-strings](https://docs.microsoft.com/en-us/windows/win32/secauthz/sid-strings):

| **Value** | **Description**                      |
| --------- | ------------------------------------ |
| “AO”      | Account operators                    |
| “RU”      | Alias to allow previous Windows 2000 |
| “AN”      | Anonymous logon                      |
| “AU”      | Authenticated users                  |
| “BA”      | Built-in administrators              |
| “BG”      | Built-in guests                      |
| “BO”      | Backup operators                     |
| “BU”      | Built-in users                       |
| “CA”      | Certificate server administrators    |
| “CG”      | Creator group                        |
| “CO”      | Creator owner                        |
| “DA”      | Domain administrators                |
| “DC”      | Domain computers                     |
| “DD”      | Domain controllers                   |
| “DG”      | Domain guests                        |
| “DU”      | Domain users                         |
| “EA”      | Enterprise administrators            |
| “ED”      | Enterprise domain controllers        |
| “WD”      | Everyone                             |
| “PA”      | Group Policy administrators          |
| “IU”      | Interactively logged-on user         |
| “LA”      | Local administrator                  |
| “LG”      | Local guest                          |
| “LS”      | Local service account                |
| “SY”      | Local system                         |
| “NU”      | Network logon user                   |
| “NO”      | Network configuration operators      |
| “NS”      | Network service account              |
| “PO”      | Printer operators                    |
| “PS”      | Personal self                        |
| “PU”      | Power users                          |
| “RS”      | RAS servers group                    |
| “RD”      | Terminal server users                |
| “RE”      | Replicator                           |
| “RC”      | Restricted code                      |
| “SA”      | Schema administrators                |
| “SO”      | Server operators                     |
| “SU”      | Service logon user                   |

Let's say that the ACE on object **A** applies to object **B**. This grants or denies object **B** access to object **A** with the specified access rights.

ACE example in SDDL format:

```
A;;RPWPCCDCLCSWRCWDWOGA;;;S-1-1-0)
​
AceType:
A = ACCESS_ALLOWED_ACE_TYPE
​
Access rights:
RP = ADS_RIGHT_DS_READ_PROP
WP = ADS_RIGHT_DS_WRITE_PROP
CC = ADS_RIGHT_DS_CREATE_CHILD
DC = ADS_RIGHT_DS_DELETE_CHILD
LC = ADS_RIGHT_ACTRL_DS_LIST
SW = ADS_RIGHT_DS_SELF
RC = READ_CONTROL
WD = WRITE_DAC
WO = WRITE_OWNER
GA = GENERIC_ALL
​
Ace Sid:
S-1-1-0
```

## Hunt for ACLs <a href="#hunt-for-acls" id="hunt-for-acls"></a>

### ActiveDirectory <a href="#activedirectory" id="activedirectory"></a>

Enumerate ACLs which `snovvcrash` user possesses against `j.doe` user:

```
(Get-ACL "AD:$((Get-ADUser j.doe).distinguishedName)").access | ? {$_.IdentityReference -eq "MEGACORP\snovvcrash"}
```

Enumerate which users possess `GenericAll` or `AllExtendedRights` permission against `j.doe` user:

```
PS > (Get-ACL "AD:$((Get-ADUser j.doe).distinguishedName)").access | ? {$_.ActiveDirectoryRights -match "GenericAll" -or $_.ActiveDirectoryRights -match "AllExtendedRights"} | select IdentityReference,ActiveDirectoryRights -Unique | ft -W
```

PowerView analog:

```
PowerView3 > Get-DomainObjectAcl -Identity j.doe -Domain megacorp.local -ResolveGUIDs | ? {$_.ActiveDirectoryRights -match "GenericAll" -or $_.ActiveDirectoryRights -match "AllExtendedRights"} | select SecurityIdentifier | sort -Property SecurityIdentifier -Unique
PowerView3 > ConvertFrom-SID <SECURITY_IDENTIFIER>
```

Find all users who can DCSync and convert their SIDs to names:

```
PowerView3 > $dcsync = Get-ObjectACL "DC=megacorp,DC=local" -ResolveGUIDs | ? {$_.ActiveDirectoryRights -match "GenericAll" -or $_.ObjectAceType -match "Replication-Get"} | select -ExpandProperty SecurityIdentifier | select -ExpandProperty value
PowerView3 > Convert-SidToName $dcsync
```

### PowerView2 <a href="#powerview2" id="powerview2"></a>

Search for interesting ACLs:

```
PowerView2 > Invoke-ACLScanner -ResolveGUIDs
```

Check if the attacker "MEGACORP\sbauer" has `GenericWrite` permissions on the "jorden" user object:

```
PowerView2 > Get-ObjectAcl -samAccountName jorden -ResolveGUIDs | ? {$_.ActiveDirectoryRights -like "*GenericWrite*" -and $_.IdentityReference -eq "MEGACORP\sbauer"}
​
InheritedObjectType   : All
ObjectDN              : CN=Jorden Mclean,OU=Athens,OU=Employees,DC=MEGACORP,DC=LOCAL  <== Victim (jorden)
ObjectType            : All
IdentityReference     : MEGACORP\sbauer  <== Attacker (sbauer)
IsInherited           : False
ActiveDirectoryRights : GenericWrite
PropagationFlags      : None
ObjectFlags           : None
InheritanceFlags      : ContainerInherit
InheritanceType       : All
AccessControlType     : Allow
ObjectSID             : S-1-5-21-3167813660-1240564177-918740779-3110
```

### PowerView3 <a href="#powerview3" id="powerview3"></a>

Search for interesting ACLs:

```
PowerView3 > Find-InterestingDomainAcl -ResolveGUIDs | ? {$_.IdentityReferenceClass -match "user"}
```

Check if the attacker "MEGACORP\sbauer" (`S-1-5-21-3167813660-1240564177-918740779-3102`) has `GenericWrite` permissions on the "jorden" user object:

```
PowerView3 > Get-DomainObjectAcl -Identity jorden -ResolveGUIDs | ? {$_.ActiveDirectoryRights -like "*GenericWrite*" -and $_.SecurityIdentifier -eq "S-1-5-21-3167813660-1240564177-918740779-3102"}
​
AceType               : AccessAllowed
ObjectDN              : CN=Jorden Mclean,OU=Athens,OU=Employees,DC=MEGACORP,DC=LOCAL
ActiveDirectoryRights : GenericWrite
OpaqueLength          : 0
ObjectSID             : S-1-5-21-3167813660-1240564177-918740779-3110  <== Victim (jorden)
InheritanceFlags      : ContainerInherit
BinaryLength          : 36
IsInherited           : False
IsCallback            : False
PropagationFlags      : None
SecurityIdentifier    : S-1-5-21-3167813660-1240564177-918740779-3102  <== Attacker (sbauer)
AccessMask            : 131112
AuditFlags            : None
AceFlags              : ContainerInherit
AceQualifier          : AccessAllowed
```

{% hint style="info" %}
The `-ResolveGUIDs` switch shows `ObjectType` and `InheritedObjectType` properties in a human readable form (not in GUIDs).
{% endhint %}

PowerView 3.0 does not return `IdentityReference` property, which makes it less handy for this task (however, you may filter the output by the attacker's SID). To automatically convert SIDs to names we can use the following loop:

```
PowerView3 > Get-DomainObjectAcl -Identity snovvcrash -ResolveGUIDs | % {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_}
```
