# ICMP Tunneling

## [Hans](https://github.com/albertzak/hanstunnel​)

{% hint style="warning" %}
Root is needed in both systems to create tun adapters and tunnels data between them using ICMP echo requests.
{% endhint %}

```text
./hans -v -f -s 1.1.1.1 -p P@ssw0rd #Start listening (1.1.1.1 is IP of the new vpn connection)
./hans -f -c <server_ip> -p P@ssw0rd -v
ping 1.1.1.100 #After a successful connection, the victim will be in the 1.1.1.100
```

## [ICMPTX](https://thomer.com/icmptx/icmptx-0.01.tar.gz)

You'll need two copies of icmptx-0.01.tar.gz; one copy for the server, one copy for the client.

Download and compile. For example:

```text
$ wget -O - https://thomer.com/icmptx/icmptx-0.01.tar.gz | tar xvfz -
$ cd icmptx-0.01/
$ make
```

### Proxy-side icmptx setup

You'll need a machine connected to the Internet to serve as your proxy. Make sure the proxy's firewall does not block ICMP traffic. If you can't simply ping the machine, icmptx will surely not work. Also, make sure your kernel supports TUN devices.

After compilation, run the icmptx server as root \(assuming the proxy's end of the tunnel is going to be 10.0.1.1\):

```text
# ./icmptx -d 10.0.1.1
```

Now verify you have a tun device:

```text
# /sbin/ifconfig tun0
tun0      Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00
          POINTOPOINT NOARP MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:10
          RX bytes:0 (0.0 b)  TX bytes:0 (0.0 b)

```

Configure the tun device. Also, ensure the kernel doesn't intercept and reply to pings.

```text
# /sbin/ifconfig tun0 mtu 65536 up 10.0.1.1 netmask 255.255.255.0 
# echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all
```

You need to enable forwarding on this server. I use iptables to implement masquerading. There are many HOWTOs about this \(a simple one, for example\). On Debian, the configuration file for iptables is in /var/lib/iptables/active. The relevant bit is:

```text
*nat
:PREROUTING ACCEPT [6:1596]
:POSTROUTING ACCEPT [1:76]
:OUTPUT ACCEPT [1:76]

-A POSTROUTING -s 10.0.0.0/8 -j MASQUERADE
COMMIT
```

Restart iptables:

```text
/etc/init.d/iptables restart
```

and enable forwarding:

```text
echo 1 > /proc/sys/net/ipv4/ip_forward
```

You can make sure this change \(and the modification that disabled echo replies\) are permanent by editing /etc/sysctl.conf, and adding:

```text
net/ipv4/ip_forward=1
net/ipv4/icmp_echo_ignore_all=1
```

### Client-side icmptx setup

The client's kernel also needs to support TUN devices. Assuming your proxy's IP address is 212.25.23.52, run as root:

```text
# ./icmptx -c 212.25.23.52
```

Now setup the tun device:

```text
# /sbin/ifconfig tun0 mtu 65536 up 10.0.1.2 netmask 255.255.255.0
```

By running /sbin/route -n, figure out what your gateway is. It's the record with the "UG" Flags field. For example:

```text

# /sbin/route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
192.168.1.0     0.0.0.0         255.255.255.0   U     0      0        0 wlan0
0.0.0.0         192.168.1.1     0.0.0.0         UG    0      0        0 wlan0
```

OK. So "192.168.1.1" is our gateway. Assuming your wireless network device is called "wlan0" \(but it might well be "eth1", or whatever\), run:

```text
# /sbin/route del default
# /sbin/route add -host 212.25.23.52 gw 192.168.1.1 dev wlan0
# /sbin/route add default gw 10.0.1.1 tun0
```

Obviously, 212.25.23.52 should be replaced with your proxy's IP address.

If all is well, you should have Internet connection now. All traffic will be tunnelled through your proxy, via ICMP.



