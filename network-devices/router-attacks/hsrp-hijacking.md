# ⭕ HSRP Hijacking

First hop redundancy protocols including [HSRP](http://en.wikipedia.org/wiki/Hot\_Standby\_Router\_Protocol), [VRRP](http://en.wikipedia.org/wiki/Virtual\_Router\_Redundancy\_Protocol), and [GLBP](http://en.wikipedia.org/wiki/Gateway\_Load\_Balancing\_Protocol) can be used to emulate a virtual router on a subnet, typically hosts' default gateway. However, if adequate authentication is not enabled, a malicious user can claim a higher priority and become the active router, which positions him to create a denial of service (DoS) or man in the middle (MITM) attack. All three protocols mentioned posess this vulnerability.

## HSRP

HSRP (Hot Standby Router Protocol) is a Cisco proprietary protocol that provides network redundancy in case of default gateway router failure. It is one of the most common protocols, however, it contains vulnerability that can lead to denial of service or to data capturing by attackers. We will show you how the HSRP attack can take place and how to protect your network against it.

![](<../../.gitbook/assets/image (275) (1) (1).png>)

In an HSRP environment, a primary router and one or more secondary routers route traffic for downstream devices. All HSRP participating routers are configured with a common virtual IP address that is set as the gateway for client devices.

Using a single virtual MAC address of 00:00:0c:07:ac:XX, where XX is the multicast group identifier, the primary device takes on responsibility for responding to ARP requests and processing network traffic while sending regular HSRP hello messages using the multicast group 224.0.0.2 with a UDP payload on port 1985. If the secondary device fails to see a preconfigured number of the hello messages, it believes the primary router has failed and takes on the primary device role. Client devices do not know which router is handling their traffic, nor do they require any special configuration to handle a failover event.

HSRP uses the concept of priorities to set the order of priority for handling network traffic. The priority field is 8 bits in length, with a highest possible priority of 255. Priorities are set by the network administrator to designate the role of each HSRP router (primary, secondary, tertiary, and so on) when the network is set up.

### HSRP Authentication

By default, HSRP uses plaintext passwords for authentication, which are included in the HSRP hello messages. The default password for HSRP configurations is "cisco," unless otherwise specified by the network administrator, as shown on this slide.

Note that because the HSRP hello messages are sent to the multicast address 224.0.0.2, all nodes on the network receive these frames. It is not necessary for an attacker to mount a MitM attack to observe the presence of HSRP traffic and the HSRP authentication data.

## HSRP Hijacking

![](<../../.gitbook/assets/image (288) (1) (1).png>)

If an attacker can observe the authentication string used for HSRP, he can mount a MitM attack by exploiting HSRP and becoming the new primary router on the network. After observing HSRP hello messages from the primary device, the attacker can identify the authentication key and the priority of the active router. By impersonating an HSRP router, the attacker can have all network traffic redirected to himself:

1. Attacker sends HSRP hello messages with a higher priority than the observed primary router.
2. After observing the attacker's HSRP hello messages, the former primary and secondary routers are demoted to secondary and tertiary status, relinquishing responsibility for the network.
3. Next, the attacker changes his network card MAC address to 00:00:0c:07:ac:XX, replacing XX with the HSRP group address observed in the hello message, and uses the IP address of the default gateway.
4. Using the IP address of the default gateway, the attacker becomes a central point for all traffic on the network and MitM before forwarding the traffic to be delivered to one of the other HSRP routers. Periodically, the attacker sends HSRP hello messages on the network to maintain its position as the primary router.

### Yersinia

Yersinia includes support for detecting the presence of HSRP traffic, revealing the source IP address of the router participating in the HSRP group, the virtual IP address, and the authentication credentials in use, as shown on this slide.

![](<../../.gitbook/assets/image (276) (1) (1) (1) (1).png>)

Press "g" to open the "Choose protocol mode" dialog; then scroll and press "Enter" on the HSRP protocol option to open the HSRP attack mode. After identifying HSRP traffic, select the target virtual IP you wish to exploit for a MitM attack and then press "x" to open the "Attack Panel" dialog, as shown. Selecting "1" will cause the attacker to become the active router but not forward traffic received, causing a DoS attack against all LAN users. Selecting "2" will implement the same attack, but the router will forward traffic to the selected HSRP member as well, creating a MitM attack.

![](<../../.gitbook/assets/image (274).png>)

### scapy

Scapy provides a very convenient interface for constructing, transmitting, and capturing packets. We can make use of its prebuilt libraries to easily construct a forged HSRP packet. Each protocol layer is built from an independent Python object:

```
root@kali:~$ sudo scapy
>>> ip = IP(src='11.11.11.11',dst='224.0.0.2')
>>> udp = UDP(sport=1985,dport=1985)
>>> hsrp = HSRP(group=1,priority=255,virtualIP='10.0.0.1')
>>> send(ip/udp/hsrp,inter=3,loop=1)
.....^C
Sent 5 packets.
```

or

```
sudo scapy
send(IP(src='11.11.11.11',dst='224.0.0.2')/UDP(sport=1985,dport=1985)/HSRP(group=1,priori
.....^C
Sent 5 packets.
```

The screenshot below is the packet capture during the attack.

![](<../../.gitbook/assets/image (300) (1) (1).png>)

If you look at frame #6, that’s when Scapy first sent the crafted HSRP message.

Shortly after seeing the crafted HSRP message, R1 sends a resign message (frame #8). This message means that R1 no longer wishes to be the active router.

Next, the R2 sends a message (frame #9) with a listen state. Yes, Wireshark displays it as passive, but if you read RFC 2281, there is no passive state.
