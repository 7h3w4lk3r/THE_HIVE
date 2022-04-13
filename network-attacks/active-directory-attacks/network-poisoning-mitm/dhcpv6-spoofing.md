# ⭕ DHCPv6 Spoofing

## <mark style="color:red;">DHCPv6 Spoofing and Poisoning</mark>

By default on Windows environments, IPv6 is enabled and has priority over IPv4. Usually, IPv6 is neither used nor configured. When a Windows machine boots or gets plugged in the network, it asks for an IPv6 configuration through a DHCPv6 request. Since DHCPv6 works in multicast, attackers on the same network can answer the DHCPv6 queries and provide the clients with a specific IP config. The IP config will include a rogue DNS server address (actually, for [mitm6](https://github.com/fox-it/mitm6), it will include two addresses, one IPv4 and one IPv6). This technique is called DHCPv6 spoofing

## <mark style="color:red;">DNS Spoofing</mark>

Attackers can then proceed to DNS spoofing. Once the clients DNS servers are set through the fake IP config pushed through DHCPv6 spoofing, each client will query the attacker's server for every domain name resolution. The attacker's server will redirect the clients to other rogue servers that will be able to capture or relay authentications.

​[mitm6](https://github.com/fox-it/mitm6) (Python) is an all-in-one tool for DHCPv6 spoofing + DNS poisoning. The following command can be run to make mitm6 redirect internal traffic only.

```
mitm6 --interface eth0 --domain $DOMAIN_FQDN
```

​[bettercap](https://www.bettercap.org) (Go) can also be used for DHCPv6 spoofing and DNS spoofing.

```
# Configure and start DHCPv6 spoofing
set dhcp6.spoof.domains $DOMAIN_FQDN
dhcp6.spoof on
​
# Configure and DNS DHCPv6 spoofing
set dns.spoof.domains $DOMAIN_FQDN
set dns.spoot.all true
dns.spoof on
```
