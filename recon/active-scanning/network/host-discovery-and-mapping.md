---
description: >-
  network mapping means visualizing the target network architecture and
  structure using the discovered hosts and/or network devices.
---

# Host discovery and mapping

## Traceroute

discovers the route that packets take between two system in the network. it helps us to construct network architecture diagrams and it is included in most if not all OSs.

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

![](../../../.gitbook/assets/tr.png)

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

{% embed url="https://hackertarget.com/online-traceroute/" %}

{% embed url="https://gsuite.tools/traceroute" %}

{% embed url="https://www.ip2location.com/free/traceroute" %}

## Best performance

for best performance use traceroute with these three options and compare the results:

```text
traceroute target
traceroute -T target
traceroute -T -p [test multiple ports] target
traceroute -I target
```

for an all in one command you can use this chain of commands:

```text
traceroute target.com > 1 && traceroute -T target.com > 2 && traceroute -I target.com > 3 && traceroute target.com -T 80 > 4 && cat 1 2 3 4 | grep -v '*' | sort -n -k 1 -u | sort -g
```

this will run a traceroute with multiple methods and combine the results for better view.

![](../../../.gitbook/assets/tr2.png)

using this method we can minimize the chance of lost hopes in the route. \( the \* signs\). here is the bash script you can use with the target domain or IP as an argument to perform the same task:

```text
#!/bin/bash 
target=$1
echo "target: &target"
traceroute $target > 1 && traceroute -T $target > 2 && traceroute -I $target > 3 && traceroute $target -T 80 > 4 && cat 1 2 3 4 | grep -v '*' | sort -n -k 1 -u | sort -g 
```

you can also use zenmap which is a GUI for nmap tool and can be downloaded from [here](https://nmap.org/zenmap/).

![](../../../.gitbook/assets/zenmap.png)

## Finding live hosts

you can use nmap, masscan or unicorn scan for this:

```text
nmap -sn -T4 -oG 192.168.1.1/24 | grep “Status: Up” | cut -f 2 -d ‘ ‘ > LiveHosts.txt
nmap –PE –sn -n 10.50.96.0/23 –oX /root/Desktop/scan.xml
```

the -PE enables ICMP Echo request host discovery \(Ping scan\)

 the -sn option means only do a host discovery and not a port scan

```text
unicornscan 192.168.100.35/24:31
masscan 192.168.2.1/24 -p80,53,443,22
```

masscan is the fastest host discovery tool available, even faster than nmap.

