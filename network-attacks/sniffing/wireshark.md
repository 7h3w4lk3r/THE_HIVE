# 🟡 Wireshark

## <mark style="color:red;">Sample pcap Captures</mark>

{% embed url="http://www.pcapr.net" %}

## <mark style="color:red;">Attack Detection</mark>

{% embed url="https://www.infosecmatter.com/detecting-network-attacks-with-wireshark" %}

## <mark style="color:red;">Useful Filters</mark>

### <mark style="color:orange;">src/dst filters</mark>

```
icmp.type
wlan.addr
src = source 
dst = destination
arp.src.proto_ipv4 = Sender IP in ARP packets
ip.addr = general filter for src and dst
eth.addr
```

### <mark style="color:orange;">http filters</mark>

```
http.request
http.response
```

### <mark style="color:orange;">tcp filters</mark>

```
tcp.analysis.ack_rtt  = Round-trip time
tcp.analysis.retransmission = Display all the retransmissions
tcp.srcport==80
tcp.dstport==80
tcp.port
```

### <mark style="color:orange;">udp filters</mark>

```
udp.port==80
udp.srcport == xx
udp.dstport == xx
```

### <mark style="color:orange;">mixed filters</mark>

```
ip.addr==192.168.1.2&&(tcp.port==53||udp.port==53)
http & ip.src == 192.168.0.1
```

### <mark style="color:orange;">flag filters</mark>

```
tcp.flags.syn
tcp.flags.syn==1 and tcp.flags.ack==0  → syn only
tcp.flags.syn==1 and tcp.flags.ack==1  → syn/ack
tcp[0xd]&2=2  → capture all the frames with the SYN bit set (SYN as well as SYN/ACK)
tcp[0xd]&18=2 → capture only SYN packets
```

## <mark style="color:red;">Display Filter Cheat Sheets</mark>

{% embed url="https://www.cellstream.com/resources/2013-09-10-11-55-21/cellstream-public-documents/wireshark-related/83-wireshark-display-filter-cheat-sheet/file" %}

{% embed url="https://medium.com/hacker-toolbelt/wireshark-filters-cheat-sheet-eacdc438969c" %}

Full Cheat Sheet

{% embed url="https://cdn.comparitech.com/wp-content/uploads/2019/06/Wireshark-Cheat-Sheet.pdf" %}
