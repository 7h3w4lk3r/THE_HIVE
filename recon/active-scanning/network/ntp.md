# NTP

NTP is the longest running, continuously operating, and a distributed application on the Internet. T he objective of NTP is simple: to allow a client to synchronize its clock with UTC \(Coordinated Universal Time, standard time scale used by most nations\), and to do so with a high degree of accuracy and a high degree of stability.

NTP \(currently version 4\) is a combination of thr ee things; first it is a software program that runs in t he background of Windows/UNIX secondly it is a protocol th at exchanges time that is been valued between servers and cl ients and finally it is a suite of algorithms that process the time values to advance or retreat the system clock. The term NTP applies to both the protocols and the client-server programs that keep running on PCs. NTP time servers work inside the TCP/IP suite and depend on User Datagram Protocol \(UDP\) port 123. NTP servers are ordinarily committed NTP devices that utilize a single time reference to which they can synchronize a system.

## How NTP works?

NTP timestamps are stored as seconds since January 1, 1900. 32 bits for the number of seconds, and 32 bits for the fractions of a second.

![](../../../.gitbook/assets/image%20%2844%29.png)

NTP assumes that the time spent on the network is the same for sending and receiving.

## Enumeration

Through NTP enumeration you can gather information such as lists of hosts connected to NTP server, IP addresses, system names, and OS running on the client system in a network. All this information can be enumerated by querying NTP server.

### nmap

```text
nmap -sU -p 123 --script ntp-info [target]
```

![](../../../.gitbook/assets/image%20%2840%29.png)

The ntp-info script can extract the remote system's timestamp against the first host and a great deal of information about the second host including kernel version and ntpd version.

As expected, a misconfigured NTP server reveals version and system details among other information which can be used to search for exploits against the vulnerable host.

```text
nmap -sU -pU:123 -Pn -n --script=ntp-monlist [target]
```

This can be automated by using a Metasploit auxiliary module

### metasploit modules

```text
auxiliary/scanner/ntp/ntp_monlist
```

This module identifies NTP servers which permit the above mentioned "monlist" queries and obtains the recent client's list. The monlist feature allows remote attackers to cause a denial of service \(traf fic amplification\) via spoofed requests. The more clients there ar e in the record, the higher the amplification













