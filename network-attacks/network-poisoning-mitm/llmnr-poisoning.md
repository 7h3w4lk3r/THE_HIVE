# ⭕ LLMNR Poisoning

## <mark style="color:red;">Windows Name Resolution</mark>

Windows infrastructure (Active Directory) depends heavily on name resolution. Attackers may be able to trick a victim into attempting authentication against them instead of a server under certain conditions.

#### windows name resolution usually goes by this order:

* Its own name?
* In NetBIOS cache?
* %Systemroot%\System32\Drivers\Etc\hosts
* DNS
* %Systemroot%\System32\Drivers\Etc\lmhosts.sam
* LLMNR
* NetBIOS

{% hint style="info" %}
#### Order varies by OS and configuration changes.
{% endhint %}

## <mark style="color:red;">LLMNR</mark>

**link local multi-casting name resolution** is used to identify hosts when DNS fails to do so .previously known as NBT-NS (netbios). key flaw is that the srvices utilize a users username and NTLMv2 hash when apropriately responded to.

![](<../../.gitbook/assets/image (204).png>)

we use the responder to sniff the request while inside the network this is the first tool to run even before nmap or nessus because we want to listen to as much traffic as we can and scanners themselves are going to generate a lot of traffic and will cause a lot of responses back to our machine.

```
responder -I vboxnet0 -rdwv
```

#### the trigger for this attack can be any wrong query in the domain for example when a user from a workstation is trying to access a share and a typo happens the DNS service will fail to locate the resource. this is when responder comes in and claims to know the resource location and asks the client for the NTLM hash. another example is when the client is pointed to the attacker machine:

![](<../../.gitbook/assets/image (211).png>)

![](<../../.gitbook/assets/image (208) (1).png>)

then we crack the hash with hashcat :

```
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt --force
```

## <mark style="color:red;">other forms of LLMNR and NBT-NS attacks</mark>

{% embed url="https://www.4armed.com/blog/llmnr-nbtns-poisoning-using-responder/" %}
