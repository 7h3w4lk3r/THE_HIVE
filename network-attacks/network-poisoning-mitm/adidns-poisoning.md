# 🟡 ADIDNS Poisoning

## <mark style="color:red;">ADIDNS</mark>

In order to function properly, Active Directory services need DNS. In that matter, Active Directory Domain Services (AD-DS) offer an integrated storage and replication service for DNS records. This is called Active Directory Integrated DNS (ADIDNS).

Just like any other domain name resolution spoofing attack, if an attacker is able to resolve requests with an arbitrary IP address, traffic gets hijacked, the attacker becomes a man-in-the-middle and further attacks can be operated.

Since ADIDNS zone DACL (Discretionary Access Control List) enables regular users to create child objects by default, attackers can leverage that and hijack traffic.

ADIDNS zones can be remotely edited

* with **dynamic updates** (a DNS specific protocol used by machine accounts to add and update their own DNS records). Users can create records if they don't exist, and they will have full control over it. By default, users that don't own a record will not be able to edit it, or to add another one with the same name, even if the type is different (A, AAAA, CNAME, MX, and so on).
* by **using LDAP** to create dnsNode objects. While dynamic updates can't be used to inject a wildcard DNS record, LDAP can be (only if the record doesn't already exist, which is the case by default).

### <mark style="color:orange;">Wildcard records</mark> <a href="#wildcard-records" id="wildcard-records"></a>

> Wildcard records allow DNS to function in a very similar fashion to LLMNR/NBNS spoofing. Once you create a wildcard record, the DNS server will use the record to answer name requests that do not explicitly match records contained in the zone. ([source](https://blog.netspi.com/exploiting-adidns/#wildcard))

​💡 In some scenarios, adding a wildcard record the the proper ADIDNS zone won't work. This is usually due to the **WINS forward lookup** being enabled on that zone. WINS forward lookup makes the DNS server send a NBT-NS Query Request to a predefined WINS server when it receives an address record query for which it doesn't know the answer. In short, it serves the same purpose as the wildcard record. This feature needs to be disabled for the wildcard record to be used.

## <mark style="color:red;">ADIDNS Poisoning</mark>

[dnstool](https://github.com/dirkjanm/krbrelayx/blob/master/dnstool.py) can be used to add, modify, query, remove, resurrect and ldapdelete records in ADIDNS:

```
# query a node
dnstool.py -u 'DOMAIN\user' -p 'password' --record '*' --action query $DomainController
​
# add a node and attach a record
dnstool.py -u 'DOMAIN\user' -p 'password' --record '*' --action add --data $AttackerIP $DomainController
```

{% hint style="info" %}
When adding records has no impact on name resolution or when the tools throw errors like `NoSuchObject`, it could be that the DNS zones in use are stored in the legacy `System` partition, or the `ForestDnsZones`, instead of the `DomainDnsZones` one.

This can be set with the `--legacy` or `--forest` option on dnstool.py, or with the `-Partition` argument for Powermad.
{% endhint %}

## <mark style="color:red;">Dynamic spoofing</mark>

Using [Inveigh](https://github.com/Kevin-Robertson/Inveigh) (Powershell), the following command will:

* operate LLMNR, NBT-NS and mDNS spoofing​
* operate ADIDNS spoofing
  * `combo` looks at LLMNR/NBNS requests and adds a record to DNS if the same request is received from multiple systems
  * `ns` injects an NS record and if needed, a target record. This is primarily for the GQBL bypass for wpad.
  * `wildcard` injects a wildcard record
* set the threshold at which the combo ADIDNS spoofing mode will take effect
* enable showing NTLM challenge/response captures from machine accounts
* set the Challenge to `1122334455667788` (to crack NTLM hashes with [crack.sh](https://crack.sh))

```
Invoke-Inveigh -ConsoleOutput Y -ADIDNS combo,ns,wildcard -ADIDNSThreshold 3 -LLMNR Y -NBNS Y -mDNS Y -Challenge 1122334455667788 -MachineAccounts Y
```

​[This wiki page](https://github.com/Kevin-Robertson/Inveigh/wiki/Basics) can be really useful to help master Inveigh and its support functions

* `Clear-Inveigh` to clear Inveigh's hashtable
* `Get-Inveigh` to get data from Inveigh's hashtable
* `Stop-Inveigh` to stop all running modules
* `Watch-Inveigh` to enable real time console output
