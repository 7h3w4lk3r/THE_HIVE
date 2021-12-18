# Wireshark

## Sample pcap Captures

{% embed url="http://www.pcapr.net" %}

## Attack Detection

{% embed url="https://www.infosecmatter.com/detecting-network-attacks-with-wireshark" %}

## Useful Filters

### src/dst filters

```
icmp.type
wlan.addr
src = source 
dst = destination
arp.src.proto_ipv4 = Sender IP in ARP packets
ip.addr = general filter for src and dst
eth.addr
```

### http filters

```
http.request
http.response
```

### tcp filters

```
tcp.analysis.ack_rtt  = Round-trip time
tcp.analysis.retransmission = Display all the retransmissions
tcp.srcport==80
tcp.dstport==80
tcp.port
```

### udp filters

```
udp.port==80
udp.srcport == xx
udp.dstport == xx
```

### mixed filters

```
ip.addr==192.168.1.2&&(tcp.port==53||udp.port==53)
http & ip.src == 192.168.0.1
```

### flag filters

```
tcp.flags.syn
tcp.flags.syn==1 and tcp.flags.ack==0  → syn only
tcp.flags.syn==1 and tcp.flags.ack==1  → syn/ack
tcp[0xd]&2=2  → capture all the frames with the SYN bit set (SYN as well as SYN/ACK)
tcp[0xd]&18=2 → capture only SYN packets
```

