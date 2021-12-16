# IPv6

## :information\_source: Introduction

#### [Internet Protocol Version 6](https://datatracker.ietf.org/doc/html/rfc8200)

{% hint style="info" %}
The presence of an inet6 entry doesn’t automatically mean the computer is accessible from external addresses.
{% endhint %}

An Ipv6 address uses 128 bits as opposed to 32 bits in IPv4.

IPv6 addresses are written using hexadecimal, as opposed to dotted decimal in IPv4

Because an hexadecimal number uses 4 bits this means that an IPv6 address consists of 32 hexadecimal numbers.

#### These numbers are grouped in 4’s giving 8 groups or blocks. The groups are written with a : (colon) as a separator.

#### `group1:group2: ……etc…. :group8`

Here is an IPv6 address example:

![](<../../.gitbook/assets/image (283) (1) (1) (1) (1).png>)

Broadcast addresses are no longer used in IPv6, replaced with multicast addresses. If an IPv6 host wants to send traffic to all the hosts on the LAN, which would have formerly used a broadcast IPv4 address and an `FF:FF:FF:FF:FF:FF` MAC address, the host uses the `FF02::1 IPv6` address with a destination MAC address of `33:33:00:00:00:01`

#### Common IPv6 addresses include the following:&#x20;

**FE80::/10** – Link local (analogous to IPv4 169.254.0.0/16 address space defined in RFC 5735 and RFC 3927)&#x20;

**FC00::/7** – Unique local address (analogous to IPv4 192.168.0.0/16, 10.0.0.0/8, and 172.16.0.0/12 address space defined in RFC 1918)&#x20;

**FF00::/8** – Multicast IPv6 traffic

**FF02::1/64** – All nodes multicast address, replacing IPv4 broadcast address 255.255.255.255

**2000::/16** – Used for stateless autoconfiguration of IPv6 addresses using the EUI 64 expansion method, leveraging the client MAC address&#x20;

**2001::/16** – Internet-wide global allocation of IPv6 address space to regional registries (analogous to the former IPv4 unique organizational address space allocations, such as 4.0.0.0/8, 64.24.0.0/16, and so on)&#x20;

**2001:db8::/32** – Used for documentation purposes

{% hint style="info" %}
Because of the length of IPv6 addresses various shortening techniques are employed.

The main technique being to omit repetitive 0’s as shown in the example above.
{% endhint %}

In IPv4 an address is split into two components a network component and a node component.

This was done initially using Address classes and later using subnet masking.

In IPv6 we do the same. The first step is to split the address into two parts.

The address is split into 2 64 bit segments the top 64 bits is the network part and the lower 64 bits the node part:

![](<../../.gitbook/assets/image (277) (1) (1) (1) (1) (1).png>)

### Linux IPv6 Interface Config

```
# Load the Linux IPv6 kernel module
modprobe ipv6

#Add an IPv6 address to eth0 with a 64-bit mask
ifconfig eth0 inet6 add fc00:660:0:1::2/64

# Remove an IPv6 address 
ifconfig eth0 inet6 del fc00:660:0:1::2/64

# Display configured IPv6 addresses
ifconfig eth0 | grep ipv6
```

### Address Types and Scope

IPv6 addresses have three types:

* **Global Unicast Address** –Scope Internet- routed on Internet
* **Unique Local** — Scope Internal Network or VPN internally routable, but **Not routed** on Internet
* **Link Local** – Scope network link- **Not Routed** internally or externally.

![](<../../.gitbook/assets/image (276) (1) (1) (1) (1) (1).png>)

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

