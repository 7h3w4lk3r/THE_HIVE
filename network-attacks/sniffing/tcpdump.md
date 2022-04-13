# ⭕ tcpdump

## <mark style="color:red;">tcpdump</mark>

```
apt install tcpdump
```

{% hint style="info" %}
when adding options like `stc, dst, port, host` ,etc. use `and` keyword between them to apply all of them
{% endhint %}

### <mark style="color:orange;">Useful options</mark>

* \-n : Host IP addresses and port numbers instead of names
* \-i \[int] : Sniff on a particular interface ( - D lists interfaces )
* \-v : Be verbose ( show TTL, IP ID, Total Length , IP options, and so on )
* \-w : Dump packets to a file ( use -r to read file later )
* \-x : Print hex
* \-X : Print hex and ASCII

#### Save the file and view the packets with this command:

```
sudo tcpdump - w - | tee file . pcap | tcpdump - r -
```

### <mark style="color:orange;">Expressions</mark>

* **Protocol:** ether, ip , ip6 , arp , rarp , tcp , udp: protocol type
* **Type:**
  * host \[host] : Only give me packets to or from that host
  * net \[network] : Only packets for a given network
  * port \[portnum] : Only packets for that port
  * portrange \[start - end] : Only packets in that range of ports
* **Direction:**
  * src : Only give me packets from that host or port
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

## <mark style="color:red;">Isolate TCP Flags</mark>

The filters below find these various packets because tcp\[13] looks at offset 13 in the TCP header, the number represents the location within the byte, and the !=0 means that the flag in question is set to 1, i.e. it’s on.

```
tcpdump 'tcp[13] & 4!=0'
tcpdump 'tcp[tcpflags] == tcp-rst'
```

#### <mark style="color:green;">Isolate TCP SYN flags</mark>

```
tcpdump 'tcp[13] & 2!=0'
tcpdump 'tcp[tcpflags] == tcp-syn'
```

#### <mark style="color:green;">Isolate packets that have both the SYN and ACK flags set.</mark>

```
tcpdump 'tcp[13]=18'
```

#### <mark style="color:green;">Isolate TCP URG flags</mark>

```
tcpdump 'tcp[13] & 32!=0'
tcpdump 'tcp[tcpflags] == tcp-urg'
```

#### <mark style="color:green;">Isolate TCP ACK flags</mark>

```
tcpdump 'tcp[13] & 16!=0'
tcpdump 'tcp[tcpflags] == tcp-ack'
```

#### <mark style="color:green;">Isolate TCP PSH flags</mark>

```
tcpdump 'tcp[13] & 8!=0'
tcpdump 'tcp[tcpflags] == tcp-psh'
```

#### <mark style="color:green;">Isolate TCP FIN flags</mark>

```
tcpdump 'tcp[13] & 1!=0'
tcpdump 'tcp[tcpflags] == tcp-fin'
```

### <mark style="color:orange;">Find HTTP User Agents</mark>

The -l switch lets you see the traffic as you’re capturing it, and helps when sending to commands like grep.

```
 tcpdump -vvAls0 | grep 'User-Agent:'
```

### <mark style="color:orange;">Cleartext GET Requests</mark>

```
 tcpdump -vvAls0 | grep 'GET'
```

### <mark style="color:orange;">Find HTTP Host Headers</mark>

```
tcpdump -vvAls0 | grep 'Host:'
```

### <mark style="color:orange;">Find HTTP Cookies</mark>

```
tcpdump -vvAls0 | grep 'Set-Cookie|Host:|Cookie:'
```

### <mark style="color:orange;">Find SSH Connections</mark>

This one works regardless of what port the connection comes in on, because it’s getting the banner response.

```
tcpdump 'tcp[(tcp[12]>>2):4] = 0x5353482D'
```

### <mark style="color:orange;">Find DNS Traffic</mark>

```
tcpdump -vvAs0 port 53
```

### <mark style="color:orange;">Find FTP Traffic</mark>

```
tcpdump -vvAs0 port ftp or ftp-data
```

## <mark style="color:red;">Find Cleartext Passwords</mark>

```
tcpdump port http or port ftp or port smtp or port imap or port pop3 or port telnet -lA | egrep -i -B5 'pass=|pwd=|log=|login=|user=|username=|pw=|passw=|passwd= |password=|pass:|user:|username:|password:|login:|pass |user '
```
