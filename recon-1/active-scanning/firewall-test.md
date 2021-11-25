# Firewall Detection & Bypass

## :information\_source: Introduction

#### [Firewalls](https://www.tutorialspoint.com/network\_security/network\_security\_firewalls.htm)

**In most cases, the main firewall is placed in the demilitarized zone ( DMZ).** Some select additional firewalls closer to the business’s intranet and/or their industrial supervisory control and data acquisition (SCADA) may also exist.

## :ballot\_box\_with\_check: Checklist <a href="next-gen-firewalls-explained" id="next-gen-firewalls-explained"></a>

* [ ] **Detection**
* [ ] **Traceroute**
* [ ] **Port Scan**
* [ ] **IPv6**
* [ ] **Banner Grab**
* [ ] **Policy Test**
* [ ] **Firewalking**
* [ ] **Product Vulnerability **

## Detection

**Usually, the presence of a firewall is detected when nmap shows some ports as filtered, but a lot of times we might face different scenarios in which the scan speed is suddenly dropped or previously live hosts are not detected as "up" in another host discovery scan.** all of these are indicators  of a change in the packet routes or target host behavior. although firewall testing is not a complicated topic, the testing scenarios can be endless depending on the firewall type and configurations.

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





### Black/White Box Tools

{% embed url="https://github.com/tcstool/Fireaway" %}

{% embed url="https://github.com/clayball/wily-possum" %}

### Port Knocking

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

### NAT Slipstreaming

NAT Slipstreaming allows an attacker to remotely access any TCP/UDP services bound to a victim machine, bypassing the victim’s NAT/firewall, just by the victim visiting a website

{% embed url="https://github.com/samyk/slipstream" %}

## Firewall Bypass

{% embed url="https://github.com/vincentcox/bypass-firewalls-by-DNS-history" %}













