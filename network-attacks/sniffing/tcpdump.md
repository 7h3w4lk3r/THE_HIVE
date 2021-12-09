# TCPDUMP

## tcpdump

```
apt install tcpdump
```

{% hint style="info" %}
when adding options like `stc, dst, port, host` ,etc. use `and` keyword between them to apply all of them
{% endhint %}

### Useful options

* \-n : Host IP addresses and port numbers instead of names
* \-i  \[int] :  Sniff on a particular interface ( - D lists interfaces )
* \-v : Be verbose ( show TTL, IP ID, Total Length , IP options, and so on )
* \-w : Dump packets to a file ( use -r to read file later )
* \-x : Print hex
* \-X : Print hex and ASCII

#### Save the file and view the packets with this command:

```
sudo tcpdump - w - | tee file . pcap | tcpdump - r -
```

### Expressions

* **Protocol:** ether, ip , ip6 , arp , rarp , tcp , udp: protocol type
* **Type:**&#x20;
  * host \[host] : Only give me packets to or from that host
  * &#x20;net \[network] : Only packets for a given network
  * &#x20;port \[portnum] : Only packets for that port&#x20;
  * portrange \[start - end] : Only packets in that range of ports
* **Direction:**&#x20;
  * src : Only give me packets from that host or port&#x20;
  * dst : Only give me packets to that host
* Use "and" or "or" to combine these together
* Use "not" to negate
* Wrap in parentheses to group elements together

#### examples:

```
# show TCP packets in ascii and hex
tcpdump -nnX tcp and dst [ip]

-------------------------------------------------------

# show all UDP packets
tcpdump -nn udp and src [ip]

------------------------------------------------------

# filter HTTPS:
tcpdump -nnSX port 443      >>>     use -c [number] to specifiy the number of packets to capture

-i [interface]   >>>    set ‘any’ for all interfaces
-vv     >>>  full protocol decode

------------------------------------------------------

# filter based on host or address:
tcpdump host [ip]   >>>  specifiy a host
tcpdump src [source ip]
tcpdump dst [destination ip]
tcpdump net 192.168.1.0/24  >>> specifiy a CIDR

-------------------------------------------------------

# filter based on port:
 tcpdump dst port  >>> specifiy src or dst port
 tcpdump src port
 
-------------------------------------------------------

#filter port range:
 tcpdump portrange 21-23

--------------------------------------------------------

# AND
and or &&

# OR
or or ||

# EXCEPT
not or !

--------------------------------------------------------

# Raw Output View
 tcpdump -ttnnvvS
 
 --------------------------------------------------------
 
# From specific IP and destined for a specific Port
 tcpdump -nnvvS src 10.5.2.3 and dst port 3389
 
 --------------------------------------------------------
 
# From One Network to Another
 tcpdump -nvX src net 192.168.0.0/16 and dst net 10.0.0.0/8 or 172.16.0.0/16
 
 --------------------------------------------------------
 
# show us all traffic going to 192.168.0.2 that is not ICMP.
 tcpdump dst 192.168.0.2 and src net and not icmp
 
 --------------------------------------------------------
 
# Traffic From a Host That Isn’t on a Specific Port
 tcpdump -vv src mars and not dst port 22
 
 --------------------------------------------------------

# Single quotes are used in order to tell tcpdump to ignore certain special characters—in this case below the “( )” brackets.
 tcpdump 'src 10.0.2.4 and (dst port 3389 or 22)'
 
```

## Isolate TCP Flags

The filters below find these various packets because tcp\[13] looks at offset 13 in the TCP header, the number represents the location within the byte, and the !=0 means that the flag in question is set to 1, i.e. it’s on.

```
tcpdump 'tcp[13] & 4!=0'
tcpdump 'tcp[tcpflags] == tcp-rst'
```

#### Isolate TCP SYN flags

```
tcpdump 'tcp[13] & 2!=0'
tcpdump 'tcp[tcpflags] == tcp-syn'
```

#### Isolate packets that have both the SYN and ACK flags set.

```
tcpdump 'tcp[13]=18'
```

#### Isolate TCP URG flags

```
tcpdump 'tcp[13] & 32!=0'
tcpdump 'tcp[tcpflags] == tcp-urg'
```

#### Isolate TCP ACK flags

```
tcpdump 'tcp[13] & 16!=0'
tcpdump 'tcp[tcpflags] == tcp-ack'
```

#### Isolate TCP PSH flags

```
tcpdump 'tcp[13] & 8!=0'
tcpdump 'tcp[tcpflags] == tcp-psh'
```

#### Isolate TCP FIN flags

```
tcpdump 'tcp[13] & 1!=0'
tcpdump 'tcp[tcpflags] == tcp-fin'
```

### Find HTTP User Agents

The -l switch lets you see the traffic as you’re capturing it, and helps when sending to commands like grep.

```
 tcpdump -vvAls0 | grep 'User-Agent:'
```

### Cleartext GET Requests

```
 tcpdump -vvAls0 | grep 'GET'
```

### Find HTTP Host Headers

```
tcpdump -vvAls0 | grep 'Host:'
```

### Find HTTP Cookies

```
tcpdump -vvAls0 | grep 'Set-Cookie|Host:|Cookie:'
```

### Find SSH Connections

This one works regardless of what port the connection comes in on, because it’s getting the banner response.

```
tcpdump 'tcp[(tcp[12]>>2):4] = 0x5353482D'
```

### Find DNS Traffic

```
tcpdump -vvAs0 port 53
```

### Find FTP Traffic

```
tcpdump -vvAs0 port ftp or ftp-data
```

## Find Cleartext Passwords

```
tcpdump port http or port ftp or port smtp or port imap or port pop3 or port telnet -lA | egrep -i -B5 'pass=|pwd=|log=|login=|user=|username=|pw=|passw=|passwd= |password=|pass:|user:|username:|password:|login:|pass |user '
```
