# Bettercap

## Official Documentation

{% embed url="https://www.bettercap.org/modules" %}

## Get Module Help

```
>> help [module name]
>> help net.recon
```

## Recon & Monitoring

#### show list of host details

```
net.show
```

#### turn passive host discovery on/off

```
net.recon on/off
```

The net.recon module performs passive scanning and is on by default, but it may not discover active devices that are not sending ARP messages while Bettercap is running. Bettercap also includes an active scanning feature in the net.probe module. Bettercap's net.probe module will continually send UDP packets to all hosts on the network. Bettercap sends the UDP activity in the form of four common protocols: NetBIOS Name Service (NBNS) discovery, Multicast DNS (MDNS), Universal Plug-and-Play (UPNP), and Web Services Discovery (WSD).

#### turn active host discovery on/off

```
net.probe on/off
```

Running the net.probe module for several seconds will typically reveal many more host discoveries than the net.recon module will discover. During the active discovery, or after stopping the discovery, you can examine information about discovered hosts using the net.show command.

## Sniffing

#### turn packet sniffing on/off

```
net.sniff on/off
```

#### set verbose

```
set net.sniff.verbose true/false
```

#### If true it will consider packets from/to this computer, otherwise it will skip them.

```
set net.sniff.local true/false
```

#### save packets to a file

```
set net.sniff.output [filename]
```

#### examine packet sniffer status

```
net.sniff stats
```

#### add source pcap file to read

```
set net.sniff.source [filename]
```

#### extract matching regex from packet payloads

```
set net.sniff.regexp [regex]
```

#### set a protocol filter for sniffer output

```
set net.sniff.filter not arp
set net.sniff.filter dhcp
```

## Fuzzing

In addition to packet-sniffing capabilities, Bettercap also can mutate packets for network protocol fuzzing using the net.fuzz module. By default, the net.fuzz module will mutate 100% of packets transmitted by Bettercap, mutating 40% of the packet payload data. You can adjust these values by changing the net.fuzz.rate and net.fuzz.ratio parameters.

[types of layers](https://github.com/google/gopacket/blob/master/layers/layertypes.go#L13)

#### mutate (fuzz) packets as they are forwarded

```
net.fuzz on/off
```

#### choose application layers are fuzzed

```
set net.fuzz.layer [layers]
```

#### change the percentage of packet and byte mutation&#x20;

```
net.fuzz.rate
net.fuzz.ratio
```

## SYN Scan

#### start scan

```
set net.show.meta true
syn.scan [ip range] [start port] [end port]
```

#### stop scan

```
syn.scan stop
```

#### show progress

```
syn.scan.progress
```

#### show results

```
//net.show
```

## Ticker Commands

The Bettercap ticker allows you to specify a collection of commands to run at a fixed frequency, which is great for monitoring the network or periodically scanning for new network targets.

```
ticker on/off
set ticker.commands "list; of; commands"

#example:
set ticker.period 10
set ticker.commands "clear; net.show; events.show 20 "
```

## ARP Spoof

start ARP spoof

```
arp.spoof on/off

# Specify a list of target IP addresses, comma-separated
set arp.spoof.targets [IPs]
```

run ARP spoof in ban mode (targets connection wont work )

```
arp.ban on/off
```

run full duplex ARP spoof (both target and gateway will be attacked)

{% hint style="info" %}
if the gateway has ARP spoof protection the attack will fail
{% endhint %}

```
set arp.spoof.fullduplex true/false 
```

## DNS Spoof

Set the dns.spoof.address for the host that you want to receive client activity stemming from spoofed responses. Set dns.spoof.domains to a comma-separated list of all the domains you want to target for DNS spoofing, or set dns.spoof.all to true to spoof all DNS responses. Alternatively, you can target your attack by setting dns.spoof.hosts to a hosts file that uses a specific hostname-to-IP-address pairing (using the same configuration of the standard /etc/hosts file on UNIX systems).

```
dns.spoof on/off
```

#### Set the IP address to return for spoofed DNS answers

```
set dns.spoof.address [address]
```

#### Set a list of domain targets for DNS spoofing, comma- separated list

```
set dns.spoof.domains [domain]
```

#### Perform DNS spoofing for all requests regardless of domain, hosts file

```
set dns.spoof.all true/false
```

#### Perform DNS spoofing only for the entries mapped in the specified hosts file

```
set dns.spoof.hosts [hostsfile]
```

## DHCPv6 Spoof

```
dhcp6.spoof on/off
```

## NDP Spoof

```
ndp.spoof on/off
```

