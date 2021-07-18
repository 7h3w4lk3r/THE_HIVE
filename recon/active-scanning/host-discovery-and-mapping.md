---
description: >-
  network mapping means visualizing the target network architecture and
  structure using the discovered hosts and/or network devices.
---

# Host discovery and mapping

## Traceroute

discovers the route that packets take between two system in the network. it helps us to construct network architecture diagrams and it is included in most of not all OSs.

### using traceroute

Windows:

```text
tracert [target]
```

Linux:

```text
traceroute [target] 
```

## Linux traceroute

#### Linux traceroute sends packets to target with varying TTLs in the IP header. by default sends UDP packets with incrementing destination ports starting from port 33434 going up by one port for each probe packet sent \( each hope measured three times \).

![](../../.gitbook/assets/tr.png)

#### here are some of the most used options:

```text
-f [number]: set the initial TTL for the first packet
-g [host list] : specify a loose source route (8 maximum hops)
-I : use ICMP echo request instead of UDP
-T : use TCP SYN instead of UDP with deafult dest port 80
-m [number] : set the maximum number of hops
-n : print numbers instead of names
-p [port] : set port ( for UDP sets the base port and increment, for TCP its fixed port
-w [number] : wait for N seconds before giving up and writing * (default 5)
-4 : force use of IPv4
-6 : force use of IPv6
```

## windows traceroute

#### sends ICMP echo request messages to target, starting with small TTLs.

some useful options:

```text
-h [number] : max number of hops (default 30)
-d : dont resolve names
-j [hostlist] : use loose source routing with a space-separated list of router IPs (up to 9 max)
-w [number] : wait for N milliseconds before givving up and writing a * (default is 4000)
-4 : force use of IPv4
-6 : force use of IPv6
```

## Web based traceroute services

#### instead of tracing from your address to target various websites allow you to trace from them to the target. so you can traceroute from around the world. by domain name or IP address. this is very useful in seeing if you are being shunned during a test.



















