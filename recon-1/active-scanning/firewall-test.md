# Firewall Detection & Bypass

## :information\_source: Introduction

#### [Firewalls](https://www.tutorialspoint.com/network\_security/network\_security\_firewalls.htm)

**In most cases, the main firewall is placed in the demilitarized zone ( DMZ).** Some select additional firewalls closer to the business’s intranet and/or their industrial supervisory control and data acquisition (SCADA) may also exist.

## :ballot\_box\_with\_check: Checklist <a href="next-gen-firewalls-explained" id="next-gen-firewalls-explained"></a>

* [ ] **Detection**
* [ ] **Policy/Rule Test**
* [ ] **IPv6**
* [ ] **Firewalking**
* [ ] **Product Vulnerability **

## Detection

**Usually, the presence of a firewall is detected when nmap shows some ports as filtered, but a lot of times we might face different scenarios in which the scan speed is suddenly dropped or previously live hosts are not detected as "up" in another host discovery scan.** all of these are indicators  of a change in the packet routes or target host behavior. although firewall testing is not a complicated topic, the testing scenarios can be endless depending on the firewall type and configurations.

## IPv6 Scan



## Policy/Rule Test

### Hping3

A great tool for packet crafting and generating custom traffic for testing firewall rules and anti-DoS systems.



**Testing ICMP: **In this example hping3 will behave like a normal ping utility, sending ICMP-echo und receiving ICMP-reply

```
hping3 -1 google.com
```



**Traceroute using ICMP**: This example is similar to famous utilities like tracert (windows) or traceroute (linux) who uses ICMP packets increasing every time in 1 its TTL value.&#x20;

```
hping3 — traceroute -V -1 testpage.com
```

we can also use the traceroute command to perform traceroute with different methods, protocols and ports. check [this section](host-discovery-and-mapping.md#traceroute) for traceroute techniques.



**Checking port:** Here hping3 will send a Syn packet to a specified port (80 in our example). We can control also from which local port will start the scan (5050).

```
hping3 — traceroute -V -S -p 80 -s 5050 testpage.com
```



**Other types of ICMP: **This example sends a ICMP address mask request ( Type 17 ).

```
hping3 -c 1 -V -1 -C 17 testpage.com
```

for testing other ICMP types check out the [`ICMP`](../../network-attacks/network/icmp-types-and-codes.md) protocol section.



**Other types of Port Scanning: **First type we will try is the FIN scan. In a TCP connection the FIN flag is used to start the connection closing routine. If we do not receive a reply, that means the port is open. Normally firewalls send a RST+ACK packet back to signal that the port is closed.

```
hping3 -c 1 -V -p 80 -s 5050 -F testpage.com
```



**Ack Scan:** This scan can be used to see if a host is alive (when Ping is blocked for example). This should send a RST response back if the port is open.

```
hping3 -c 1 -V -p 80 -s 5050 -A testpage.com
```



**Xmas Scan:** This scan sets the sequence number to zero and set the URG + PSH + FIN flags in the packet. If the target device’s TCP port is closed, the target device sends a TCP RST packet in reply. If the target device’s TCP port is open, the target discards the TCP Xmas scan, sending no reply.

```
hping3 -c 1 -V -p 80 -s 5050 -M 0 -UPF testpage.com
```



**Null Scan: **This scan sets the sequence number to zero and have no flags set in the packet. If the target device’s TCP port is closed, the target device sends a TCP RST packet in reply. If the target device’s TCP port is open, the target discards the TCP NULL scan, sending no reply.

```
hping3 -c 1 -V -p 80 -s 5050 -Y testpage.com
```



**Smurf Attack: **This is a type of denial-of-service attack that floods a target system via spoofed broadcast ping messages.

```
hping3 -1 — flood -a VICTIM_IP BROADCAST_ADDRESS
```

**DOS Land Attack:**

```
hping3 -V -c 1000000 -d 120 -S -w 64 -p 445 -s 445 — flood — rand-source VICTIM_IP
```

#### useful hping3 options:

```
# MODE   default mode     TCP
  -0  --rawip      RAW IP mode
  -1  --icmp       ICMP mode
  -2  --udp        UDP mode
  -8  --scan       SCAN mode.
                   Example: hping --scan 1-30,70-90 -S www.target.host
  -9  --listen     listen mode
  -i  --interval  wait (uX for X microseconds, for example -i u1000)
  --fast      alias for -i u10000 (10 packets for second)
  --faster    alias for -i u1000 (100 packets for second)
  --flood	   sent packets as fast as possible. Don't show replies.
  -q  --quiet     quiet
  -I  --interface interface name (otherwise default routing interface)
  -V  --verbose   verbose mode
  -D  --debug     debugging info

# IP
  -a  --spoof      spoof source address
  --rand-dest      random destionation address mode. see the man.
  --rand-source    random source address mode. see the man.
  -t  --ttl        ttl (default 64)
  -N  --id         id (default random)
  -W  --winid      use win* id byte ordering
  -r  --rel        relativize id field          (to estimate host traffic)
  -f  --frag       split packets in more frag.  (may pass weak acl)
  -x  --morefrag   set more fragments flag
  -y  --dontfrag   set don't fragment flag
  -g  --fragoff    set the fragment offset
  -m  --mtu        set virtual mtu, implies --frag if packet size > mtu
  -o  --tos        type of service (default 0x00), try --tos help
  -G  --rroute     includes RECORD_ROUTE option and display the route buffer

# FLAGS
  -M  --setseq     set TCP sequence number
  -L  --setack     set TCP ack
  -F  --fin        set FIN flag
  -S  --syn        set SYN flag
  -R  --rst        set RST flag
  -P  --push       set PUSH flag
  -A  --ack        set ACK flag
  -U  --urg        set URG flag
  -X  --xmas       set X unused flag (0x40)
  -Y  --ymas       set Y unused flag (0x80)

# TCP/UDP OPTIONS
  -b  --badcksum   (try to) send packets with a bad IP checksum
  -O  --tcpoff     set fake tcp data offset     (instead of tcphdrlen / 4)
  -s  --baseport   base source port             (default random)
  -p  --destport   [+][+]<port> destination port(default 0) ctrl+z inc/dec
  -w  --win        winsize (default 64) 
  --tcp-timestamp  enable the TCP timestamp option to guess the HZ/uptime

# ICMP
  -C  --icmptype   icmp type (default echo request)
  -K  --icmpcode   icmp code (default 0)
      --force-icmp send all icmp types (default send only supported types)
      --icmp-gw    set gateway address for ICMP redirect (default 0.0.0.0)
     --icmp-help  display help for others icmp options

# OTHER OPTIONS
  -d  --data       data size                    (default is 0)
  -E  --file       data from file
  -e  --sign       add 'signature'
  -j  --dump       dump packets in hex
  -J  --print      dump printable characters
  -B  --safe       enable 'safe' protocol
```

### nmap

**fragmentation :** The -f command induces our scan to deploy diminutive fragmented IP packets. Specifically, our command utilizes 16 bytes per fragment which diminishes the number of fragments. Fragmented packets is one of them and consist in sending several tiny packets instead of one normal size packet.

You can use fragmented packets with Nmap using the "-f" option, however, nowadays most firewall and IDS detect fragmented packets.

```
nmap -f 192.168.1.12
```

****

**custom offset size :**  nmap `--mtu` command allows us to specify our own offset size. Remember that the offset size has to be a multiple of 16.

* Nmap is giving the option to the user to set a specific MTU (Maximum Transmission Unit) to the packet.
* This is similar to the packet fragmentation technique.
* During the scan, Nmap will create packets with a size based on the number that we will give.
* In this example, we gave the number 24, so the Nmap will create 24-byte packets, causing confusion to the firewall.
* Keep in mind that the MTU number must be a multiple of 8 (8, 16, 24, 32, etc.).

```
nmap --badsum 192.168.1.12
```

**bad checksum :** Badsum command induces the deployment of an invalid TCP/UDP/SCTP checksum for packets transmitted to our target. As practically every host IP stack would correctly drop the packets, each response accepted is possibly originating from a firewall or Intrusion Detection System that wasn’t concerned with confirming the checksum. Additionally, we try to use some scripts from the Nmap NSE like "firewall-bypass," but I should warn you that results from the usage of this script can be a false positive with a high percentage.

```
nmap --badsum 192.168.1.12
```

**helper bypass :** This script detects a vulnerability in Netfilter and other firewalls that use helpers to dynamically open ports for protocols such as FTP and sip (in our case, we also combine it with stealth scan). The script works by spoofing a packet from the target server asking for opening a related connection to a target port, which will be fulfilled by the firewall through the adequate protocol helper port. The attacking machine should be on the same network segment as the firewall for this to work. The script supports FTP helper on both IPv4 and IPv6. Real path filter is used to prevent such attacks.

```
map -sS -T5 192.168.1.12 --script firewall-bypass
```

**firewalk: **Tries to discover firewall rules using an IP TTL expiration technique known as firewalking.To determine a rule on a given gateway, the scanner sends a probe to a metric located behind the gateway, with a TTL one higher than the gateway. If the probe is forwarded by the gateway, then we can expect to receive an ICMP\_TIME\_EXCEEDED reply from the gateway next hop router, or eventually the metric itself if it is directly connected to the gateway. Otherwise, the probe will timeout.

It starts with a TTL equals to the distance to the target. If the probe timeout, then it is resent with a TTL decreased by one. If we get an ICMP\_TIME\_EXCEEDED, then the scan is over for this probe.

Every "no-reply" filtered TCP and UDP ports are probed. As for UDP scans, this process can be quite slow if lots of ports are blocked by a gateway close to the scanner.

Scan parameters can be controlled using the firewalk.\* optional arguments.

From an original idea of M. Schiffman and D. Goldsmith, authors of the firewalk tool.

```
# script arguments:

# maximum number of ports to probe per protocol. Set to -1 to scan every filtered port.
firewalk.max-probed-ports

# the maximum number of allowed retransmissions.
firewalk.max-retries

# the duration of the packets capture loop (in milliseconds).
firewalk.recv-timeout

# maximum number of parallel active probes.
firewalk.max-active-probes

# validity period of a probe (in milliseconds).
firewalk.probe-timeout

# examples:
nmap --script=firewalk --traceroute <host>
nmap --script=firewalk --traceroute --script-args=firewalk.max-retries=1 <host>
nmap --script=firewalk --traceroute --script-args=firewalk.probe-timeout=400ms <host>
nmap --script=firewalk --traceroute --script-args=firewalk.max-probed-ports=7 <host>
```

**Custom Scan Types : **The --scanflags option allows you to design your own scan by specifying arbitrary TCP flags. Let your creative juices flow, while evading intrusion detection systems whose vendors simply paged through the Nmap man page adding specific rules! The --scanflags argument can be a numerical flag value such as 9 (PSH and FIN), but using symbolic names is easier. Just mash together any combination of URG, ACK, PSH, RST, SYN, and FIN. For example, --scanflags URGACKPSHRSTSYNFIN sets everything, though it's not very useful for scanning. The order these are specified in is irrelevant.

In addition to specifying the desired flags, you can specify a TCP scan type (such as -sA or -sF). That base type tells Nmap how to interpret responses. For example, a SYN scan considers no-response indicative of a filtered port, while a FIN scan treats the same as open|filtered. Nmap will behave the same way it does for the base scan type, except that it will use the TCP flags you specify instead. If you don't specify a base type, SYN scan is used.

```
--scanflags <flags>: Customize TCP scan flags

# examples:

# SYN/FIN
nmap -sS --scanflags SYNFIN [ip]

# PSH
nmap -sF --scanflags PSH [ip]
```

### Scapy

{% embed url="https://resources.infosecinstitute.com/topic/port-scanning-using-scapy" %}

{% file src="../../.gitbook/assets/Scapy 802.11 Cheat Sheet v0.1.pdf" %}

{% file src="../../.gitbook/assets/ScapyCheatSheet_v0.2.pdf" %}

{% embed url="https://0xbharath.github.io/art-of-packet-crafting-with-scapy/index.html" %}

{% embed url="http://blog.facilelogin.com/2010/12/hand-crafting-tcp-handshake-with-scapy.html" %}

## Port Knocking

Port-knocking the a obfuscation-as-security technique. I basically means that after knocking on ports in a specific sequence a certain port will open automatically. It seems to be more popular in Capture-the-flag contests than real life networks. But I have included it anyways, since CTF:s are great.

This is a way to hide certain ports, so you don't get unwanted intrusion-intents.

When you "knock" on a port you are really just sending TCP-packets with `SYN`-flag to that port. The closed port will then respond with a `ACK/RST`. Which basically means that the host has received the `TCP`-packet, and it ACKnolwdge it, but responds with a Reset (`RST`) flag. `RST` just means that the port is closed.

#### Knock

```
apt install knockd
knock [targert ip] [port]
knock 192.168.56.101 4000 5000 6000
```

rescan the system to see if any ports are now open, try to connect to new ports:

```
nc 192.168.1.102 8888
```

#### nmap

```
for x in 4000 5000 6000; do nmap -Pn --host_timeout 201 --max-retries 0 -p $x server_ip_address; done
```

#### Netcat

```
nc 192.168.1.102 4000
nc 192.168.1.102 5000
nc 192.168.1.102 6000
nc 192.168.1.102 8888
```

## NAT Slipstreaming

NAT Slipstreaming allows an attacker to remotely access any TCP/UDP services bound to a victim machine, bypassing the victim’s NAT/firewall, just by the victim visiting a website

{% embed url="https://github.com/samyk/slipstream" %}

## Bypass by DNS History

{% embed url="https://github.com/vincentcox/bypass-firewalls-by-DNS-history" %}

## Ohter Tools

{% embed url="https://github.com/tcstool/Fireaway" %}

{% embed url="https://github.com/clayball/wily-possum" %}
