# 🔴 Firewall Evasion

## :information\_source: <mark style="color:red;">Introduction</mark>

#### [Firewalls](https://www.tutorialspoint.com/network\_security/network\_security\_firewalls.htm)

**In most cases, the main firewall is placed in the demilitarized zone ( DMZ).** Some select additional firewalls closer to the business’s intranet and/or their industrial supervisory control and data acquisition (SCADA) may also exist.

## :ballot\_box\_with\_check: <mark style="color:red;">Checklist</mark> <a href="#next-gen-firewalls-explained" id="next-gen-firewalls-explained"></a>

* [ ] [**Detection**](firewall-ids-evasion.md#detection)
* [ ] [**Policy/Rule Test**](firewall-ids-evasion.md#policy-rule-test)
* [ ] [**Scan Timing**](firewall-ids-evasion.md#scan-timing)
* [ ] [**IPv6**](../network-attacks/network/ipv6.md)
* [ ] [**Firewalking**](firewall-ids-evasion.md#fire-walking)

## <mark style="color:red;">Detection</mark>

**Usually, the presence of a firewall is detected when nmap shows some ports as filtered, but a lot of times we might face different scenarios in which the scan speed is suddenly dropped or previously live hosts are not detected as "up" in another host discovery scan.** all of these are indicators of a change in the packet routes or target host behavior. although firewall testing is not a complicated topic, the testing scenarios can be endless depending on the firewall type and configurations.

## <mark style="color:red;">Policy/Rule Test</mark>

### <mark style="color:orange;">Hping3</mark>

A great tool for packet crafting and generating custom traffic for testing firewall rules and anti-DoS systems.

**Testing ICMP:** In this example hping3 will behave like a normal ping utility, sending ICMP-echo und receiving ICMP-reply

```
hping3 -1 google.com
```

<mark style="color:green;">**Traceroute using ICMP**</mark><mark style="color:green;">:</mark> This example is similar to famous utilities like tracert (windows) or traceroute (linux) who uses ICMP packets increasing every time in 1 its TTL value.

```
hping3 traceroute -V -1 testpage.com
```

we can also use the traceroute command to perform traceroute with different methods, protocols and ports. check [this section](active-scanning/host-discovery-and-mapping.md#traceroute) for traceroute techniques.

**Checking port:** Here hping3 will send a Syn packet to a specified port (80 in our example). We can control also from which local port will start the scan (5050).

```
hping3 — traceroute -V -S -p 80 -s 5050 testpage.com
```

<mark style="color:green;">**Other types of ICMP:**</mark> This example sends a ICMP address mask request ( Type 17 ).

```
hping3 -c 1 -V -1 -C 17 testpage.com
```

for testing other ICMP types check out the [`ICMP`](../network-attacks/broken-reference/) protocol section.

<mark style="color:green;">**Other types of Port Scanning:**</mark> First type we will try is the FIN scan. In a TCP connection the FIN flag is used to start the connection closing routine. If we do not receive a reply, that means the port is open. Normally firewalls send a RST+ACK packet back to signal that the port is closed.

```
hping3 -c 1 -V -p 80 -s 5050 -F testpage.com
```

<mark style="color:green;">**Ack Scan:**</mark> This scan can be used to see if a host is alive (when Ping is blocked for example). This should send a RST response back if the port is open.

```
hping3 -c 1 -V -p 80 -s 5050 -A testpage.com
```

<mark style="color:green;">**Xmas Scan:**</mark> This scan sets the sequence number to zero and set the URG + PSH + FIN flags in the packet. If the target device’s TCP port is closed, the target device sends a TCP RST packet in reply. If the target device’s TCP port is open, the target discards the TCP Xmas scan, sending no reply.

```
hping3 -c 1 -V -p 80 -s 5050 -M 0 -UPF testpage.com
```

<mark style="color:green;">**Null Scan:**</mark> This scan sets the sequence number to zero and have no flags set in the packet. If the target device’s TCP port is closed, the target device sends a TCP RST packet in reply. If the target device’s TCP port is open, the target discards the TCP NULL scan, sending no reply.

```
hping3 -c 1 -V -p 80 -s 5050 -Y testpage.com
```

<mark style="color:green;">**Smurf Attack:**</mark> This is a type of denial-of-service attack that floods a target system via spoofed broadcast ping messages.

```
hping3 -1 — flood -a VICTIM_IP BROADCAST_ADDRESS
```

<mark style="color:green;">**DOS Land Attack:**</mark>

```
hping3 -V -c 1000000 -d 120 -S -w 64 -p 445 -s 445 — flood — rand-source VICTIM_IP
```

#### <mark style="color:green;">useful hping3 options:</mark>

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

### <mark style="color:orange;">nmap</mark>

<mark style="color:green;">**fragmentation :**</mark> The -f command induces our scan to deploy diminutive fragmented IP packets. Specifically, our command utilizes 16 bytes per fragment which diminishes the number of fragments. Fragmented packets is one of them and consist in sending several tiny packets instead of one normal size packet.

You can use fragmented packets with Nmap using the "-f" option, however, nowadays most firewall and IDS detect fragmented packets.

```
nmap -f 192.168.1.12
```

***

<mark style="color:green;">**custom offset size :**</mark> nmap `--mtu` command allows us to specify our own offset size. Remember that the offset size has to be a multiple of 16.

* Nmap is giving the option to the user to set a specific MTU (Maximum Transmission Unit) to the packet.
* This is similar to the packet fragmentation technique.
* During the scan, Nmap will create packets with a size based on the number that we will give.
* In this example, we gave the number 24, so the Nmap will create 24-byte packets, causing confusion to the firewall.
* Keep in mind that the MTU number must be a multiple of 8 (8, 16, 24, 32, etc.).

```
nmap --badsum 192.168.1.12
```

<mark style="color:green;">**bad checksum :**</mark> Badsum command induces the deployment of an invalid TCP/UDP/SCTP checksum for packets transmitted to our target. As practically every host IP stack would correctly drop the packets, each response accepted is possibly originating from a firewall or Intrusion Detection System that wasn’t concerned with confirming the checksum. Additionally, we try to use some scripts from the Nmap NSE like "firewall-bypass," but I should warn you that results from the usage of this script can be a false positive with a high percentage.

```
nmap --badsum 192.168.1.12
```

<mark style="color:green;">**helper bypass :**</mark> This script detects a vulnerability in Netfilter and other firewalls that use helpers to dynamically open ports for protocols such as FTP and sip (in our case, we also combine it with stealth scan). The script works by spoofing a packet from the target server asking for opening a related connection to a target port, which will be fulfilled by the firewall through the adequate protocol helper port. The attacking machine should be on the same network segment as the firewall for this to work. The script supports FTP helper on both IPv4 and IPv6. Real path filter is used to prevent such attacks.

```
map -sS -T5 192.168.1.12 --script firewall-bypass
```

<mark style="color:green;">**Avoiding signatures:**</mark> add garbage data to the packets so the IPS/IDS signature is avoided.

```
--data-length 25
```

<mark style="color:green;">**firewalk:**</mark> Tries to discover firewall rules using an IP TTL expiration technique known as firewalking.To determine a rule on a given gateway, the scanner sends a probe to a metric located behind the gateway, with a TTL one higher than the gateway. If the probe is forwarded by the gateway, then we can expect to receive an ICMP\_TIME\_EXCEEDED reply from the gateway next hop router, or eventually the metric itself if it is directly connected to the gateway. Otherwise, the probe will timeout.

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

<mark style="color:green;">**Custom Scan Types :**</mark> The --scanflags option allows you to design your own scan by specifying arbitrary TCP flags. Let your creative juices flow, while evading intrusion detection systems whose vendors simply paged through the Nmap man page adding specific rules! The --scanflags argument can be a numerical flag value such as 9 (PSH and FIN), but using symbolic names is easier. Just mash together any combination of URG, ACK, PSH, RST, SYN, and FIN. For example, --scanflags URGACKPSHRSTSYNFIN sets everything, though it's not very useful for scanning. The order these are specified in is irrelevant.

In addition to specifying the desired flags, you can specify a TCP scan type (such as -sA or -sF). That base type tells Nmap how to interpret responses. For example, a SYN scan considers no-response indicative of a filtered port, while a FIN scan treats the same as open|filtered. Nmap will behave the same way it does for the base scan type, except that it will use the TCP flags you specify instead. If you don't specify a base type, SYN scan is used.

```
--scanflags <flags>: Customize TCP scan flags

# examples:

# SYN/FIN
nmap -sS --scanflags SYNFIN [ip]

# PSH
nmap -sF --scanflags PSH [ip]
```

### <mark style="color:orange;">Scapy</mark>

{% embed url="https://resources.infosecinstitute.com/topic/port-scanning-using-scapy" %}

{% file src="../.gitbook/assets/Scapy 802.11 Cheat Sheet v0.1.pdf" %}

{% file src="../.gitbook/assets/ScapyCheatSheet_v0.2 (2).pdf" %}

{% embed url="https://0xbharath.github.io/art-of-packet-crafting-with-scapy/index.html" %}

{% embed url="http://blog.facilelogin.com/2010/12/hand-crafting-tcp-handshake-with-scapy.html" %}

## <mark style="color:red;">Scan Timing</mark>

### <mark style="color:orange;">nmap Timing Options</mark>

| **Description**                    | **Option \[flag]**        |
| ---------------------------------- | ------------------------- |
| Timing Templates                   | **-T\[0-5]**              |
| Set the Packet Time To Live \[TTL] | **–ttl**                  |
| Minimum # of Parallel Operations   | **–min-parallelism**      |
| Maximum # of Parallel Operations   | **–max-parallelism**      |
| Minimum Host Group Size            | **–min-hostgroup**        |
| Maximum Host Group Size            | **–max-hostgroup**        |
| Maximum RTT Timeout                | **–max-rtt-timeout**      |
| Initial RTT Timeout                | **–initial-rtt-timeout**  |
| Maximum Retries                    | **–max-retries**          |
| Host Timeout                       | **–host-timeout**         |
| Minimum Scan Delay                 | **–scan-delay**           |
| Maximum Scan Delay                 | **–max-scan-delay**       |
| Minimum Packet Rate                | **–min-rate**             |
| Maximum Packet Rate                | **–max-rate**             |
| Defeat Reset Rate Limits           | **–defeat-rst-ratelimit** |

### <mark style="color:orange;">**NMAP Timing Unit Flags**</mark>

By default, NMAP executes time units in seconds. However, by applying a qualifier to the timing flag, we can instruct NMAP to accept timing units in milliseconds, minutes, or hours – as seen in **Table 1.2** below.

| **Flag**   | **Definition**                    | **Time Unit**      | **Flag** |
| ---------- | --------------------------------- | ------------------ | -------- |
| **(none)** | Milliseconds (1/1000 of a second) | 60000 milliseconds | 60000ms  |
| **s**      | Seconds                           | 60 seconds         | 60s      |
| **m**      | Minutes                           | 1 minutes          | 1m       |
| **h**      | Hours                             | 1 hour             | 1h       |

***

For example, we can instruct NMAP to scan a target for a 1 minute before aborting using the **–host-timeout** option as shown below:

```
nmap – host-timeout 60000 192.168.130.132
```

The above command can be also executed as:

```
nmap – host-timeout 60s 192.168.130.132
```

Or:

```
nmap – host-timeout 1m 192.168.130.132
```

### <mark style="color:orange;">**NMAP Timing Templates**</mark>

**Flag:** -T

**Syntax:** nmap -T\[Template No.] \[Target]

**Description:** Specify an NMAP timing template for a scan.

Think of NMAP timing templates as shortcuts for different timing options.

NMAP provides six templates \[0 to 5] we can use to slow down scanning \[evade firewalls] or speed up \[get faster results] – depending on the scanning scenario, as seen in **Table 1.3** below.

| **NMAP Timing Template** | **Name**        | **Description**                              |
| ------------------------ | --------------- | -------------------------------------------- |
| **-T0**                  | Paranoid scan   | A very slow scan                             |
| **-T1**                  | Sneaky scan     | Excellent for avoiding firewalls             |
| **-T2**                  | Polite scan     | Unlikely to interfere with the target system |
| **-T3**                  | Normal scan     | The default NMAP timing template             |
| **-T4**                  | Aggressive scan | Provides faster results on LANs              |
| **-T5**                  | Insane scan     | A fast aggressive scan                       |

{% embed url="https://nmap.org/book/man-performance.html" %}

## <mark style="color:red;">Fire Walking</mark>

Fire walking is the method of determining the movement of a data packet from an untrusted external host to a protected internal host through a firewall.\
\
The idea behind fire walking is to determine which ports are open and whether packets with control information can pass through a packet filtering device.

### <mark style="color:orange;">firewalk</mark> <a href="#techopedia-explains-fire-walking" id="techopedia-explains-fire-walking"></a>

Firewalk is an active reconnaissance network security tool that attempts to determine what layer 4 protocols a given IP forwarding device will pass. It works by sending out TCP or UDP packets with a TTL one hop greater than the targeted gateway. If the gateway allows the traffic, it will forward the packets to the next hop where they will expire and elicit an ICMP\_TIME\_EXCEEDED message. Otherwise, it will likely drop the packets and there will be no response.

To get the correct IP TTL that will result in packets expiring one hop beyond the gateway, Firewalk needs to ramp up hop counts. It does this in the same manner that traceroute works. Once the scan is `bound` (that is, Firewalk knows the gateway hop count), it begins the scan. The ultimate destination host does not have to be reached, it only needs to be somewhere downstream, on the other side of the gateway, from the scanning host.

Firewalk helps in assessing the security configuration of packet filtering devices, such as those used in firewall systems. It is relevant for network security assessments, like network penetration tests (pentests).

<mark style="color:green;">Scan ports 8079-8081 (</mark><mark style="color:green;">`-S8079-8081`</mark><mark style="color:green;">) through the eth0 interface (</mark><mark style="color:green;">`-i eth0`</mark><mark style="color:green;">), do not resolve hostnames (</mark><mark style="color:green;">`-n`</mark><mark style="color:green;">), use TCP (</mark><mark style="color:green;">`-pTCP`</mark><mark style="color:green;">) via the gateway (</mark><mark style="color:green;">`192.168.1.1`</mark><mark style="color:green;">) against the target IP (</mark><mark style="color:green;">`192.168.0.1`</mark><mark style="color:green;">):</mark>

```
firewalk -S8079-8081  -i eth0 -n -pTCP 192.168.1.1 192.168.0.1
```



## <mark style="color:red;">Port Knocking</mark>

Port-knocking the a obfuscation-as-security technique. I basically means that after knocking on ports in a specific sequence a certain port will open automatically. It seems to be more popular in Capture-the-flag contests than real life networks. But I have included it anyways, since CTF:s are great.

This is a way to hide certain ports, so you don't get unwanted intrusion-intents.

When you "knock" on a port you are really just sending TCP-packets with `SYN`-flag to that port. The closed port will then respond with a `ACK/RST`. Which basically means that the host has received the `TCP`-packet, and it ACKnolwdge it, but responds with a Reset (`RST`) flag. `RST` just means that the port is closed.

### <mark style="color:orange;">Knock</mark>

```
apt install knockd
knock [targert ip] [port]
knock 192.168.56.101 4000 5000 6000
```

rescan the system to see if any ports are now open, try to connect to new ports:

```
nc 192.168.1.102 8888
```

### <mark style="color:orange;">nmap</mark>

```
for x in 4000 5000 6000; do nmap -Pn --host_timeout 201 --max-retries 0 -p $x server_ip_address; done
```

### <mark style="color:orange;">Netcat</mark>

```
nc 192.168.1.102 4000
nc 192.168.1.102 5000
nc 192.168.1.102 6000
nc 192.168.1.102 8888
```

## <mark style="color:red;">NAT Slipstreaming</mark>

NAT Slipstreaming allows an attacker to remotely access any TCP/UDP services bound to a victim machine, bypassing the victim’s NAT/firewall, just by the victim visiting a website

{% embed url="https://github.com/samyk/slipstream" %}

## <mark style="color:red;">Bypass by DNS History</mark>

{% embed url="https://github.com/vincentcox/bypass-firewalls-by-DNS-history" %}

## <mark style="color:red;">Ohter Tools</mark>

{% embed url="https://github.com/tcstool/Fireaway" %}

{% embed url="https://github.com/clayball/wily-possum" %}

## <mark style="color:red;">Other Resources</mark>

{% embed url="https://medium.com/@IamLucif3r/top-10-firewall-ids-evasion-techniques-cb1e1cc06f24" %}

{% embed url="https://nmap.org/book/man-bypass-firewalls-ids.html" %}

{% embed url="https://pentestlab.blog/tag/firewall-evasion" %}
