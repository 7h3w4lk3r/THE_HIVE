# Firewall Detection & Bypass

## :information\_source: Introduction

[What is a Firewall ?](https://airoserver.com/blog/everything-about-firewall/)

**In most cases, the main firewall is placed in the demilitarized zone ( DMZ).** Some select additional firewalls closer to the business’s intranet and/or their industrial supervisory control and data acquisition (SCADA) may also exist.

**Next Gen Firewalls :** The main flaw within traditional firewall models is that they can’t take part in stateful packet inspection. Instead, they go about merely analyzing the network’s current traffic via IP addresses and the packet’s port numbers, which means zero consideration to the earlier traffic that passed through it.

Next Gen Firewalls, or NGFW, represents progress. With them, all active connections are monitored alongside the state of the connections, and dynamic packet filtering takes place. The result is more comprehensive access determination









## Firewall Test

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













