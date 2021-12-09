# IPv6

## :information\_source: Introduction

#### [Internet Protocol Version 6](https://datatracker.ietf.org/doc/html/rfc8200)

{% hint style="info" %}
The presence of an inet6 entry doesn’t automatically mean the computer is accessible from external addresses.
{% endhint %}

An Ipv6 address uses 128 bits as opposed to 32 bits in IPv4.

IPv6 addresses are written using hexadecimal, as opposed to dotted decimal in IPv4

Because an hexadecimal number uses 4 bits this means that an IPv6 address consists of 32 hexadecimal numbers.

These numbers are grouped in 4’s giving 8 groups or blocks. The groups are written with a : (colon) as a separator.

group1:group2: ……etc…. :group8

Here is an IPv6 address example:

![](<../../.gitbook/assets/image (283) (1) (1) (1).png>)

{% hint style="info" %}
Because of the length of IPv6 addresses various shortening techniques are employed.

The main technique being to omit repetitive 0’s as shown in the example above.
{% endhint %}

In IPv4 an address is split into two components a network component and a node component.

This was done initially using Address classes and later using subnet masking.

In IPv6 we do the same. The first step is to split the address into two parts.

The address is split into 2 64 bit segments the top 64 bits is the network part and the lower 64 bits the node part:

![](<../../.gitbook/assets/image (277) (1) (1) (1) (1).png>)

### Address Types and Scope

IPv6 addresses have three types:

* **Global Unicast Address** –Scope Internet- routed on Internet
* **Unique Local** — Scope Internal Network or VPN internally routable, but **Not routed** on Internet
* **Link Local** – Scope network link- **Not Routed** internally or externally.

![](<../../.gitbook/assets/image (276) (1) (1).png>)

### IPv6 Loop Back

The IPv6 loopback address is ::1. You can ping it as follows:

```
ping ::1
```



## Enumeration

### nmap

```
nmap -6 -sV [ip] --version-all --max-retries 3 -T4 -Pn -n --reason –vvv 
```

```
# NSE scripts

ipv6-multicast-mld-list.nse
ipv6-node-info.nse
ipv6-ra-flood.nse
targets-ipv6-map4to6.nse
targets-ipv6-multicast-echo.nse
targets-ipv6-multicast-invalid-dst.nse
targets-ipv6-multicast-mld.nse
targets-ipv6-multicast-slaac.nse
targets-ipv6-wordlist.nse

```

### metasploit

```
search ipv6

# auxiliary modules

   auxiliary/scanner/discovery/ipv6_multicast_ping
   auxiliary/scanner/discovery/ipv6_neighbor
   auxiliary/scanner/discovery/ipv6_neighbor_router_advertisement

```

## Exploitation

{% embed url="https://www.kali.org/tools/thc-ipv6" %}

{% embed url="https://www.kali.org/tools/ipv6-toolkit" %}

