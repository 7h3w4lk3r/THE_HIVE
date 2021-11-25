# IPv6

## IPv6

#### [Internet Protocol Version 6](https://datatracker.ietf.org/doc/html/rfc8200)

{% hint style="info" %}
The presence of an inet6 entry doesn’t automatically mean the computer is accessible from external addresses.
{% endhint %}

In IPv6 the NS (Neighbor Solicitation) and NA (Neighbor Advertisement) replace the ARP protocol functionality. the NS is used to resolve and address, so it sends multicast packets. The NA is unicast as is used to answer the NS. A NA packet could also be sent without needing a NS packet.

{% hint style="info" %}
0:0:0:0:0:0:0:1 = 1 – This is 127.0.0.1 equivalent in IPv4.
{% endhint %}

Link-local Addresses: These are private address that is not meant to be routed on the internet. They can be used locally by private or temporary LANs for sharing and distribution of file among devices on the LAN. Other devices in your local LAN using this kind of addresses can be found sending a pig to the multicast address ff02::01

{% hint style="info" %}
FE80::/10 – Link-local unicast address range.
{% endhint %}

```
ping6 –I eth0 -c 5 ff02::1 > /dev/null 2>&1
ip neigh | grep ^fe80

#Or you could also use
alive6 eth0
```

If you know the MAC address of a host in the same net as you (you could just ping its ipv4 address and view the arp table to found its MAC address), you can calculate his Link-local address to communicate with him.

