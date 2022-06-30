# Router Advertisement Flooding

## <mark style="color:red;">Router Advertisement</mark>

#### An attacker can introduce an IPv6 router on the network by responding to IPv6 client Router Solicitation (RS) messages:

1. IPv6 nodes will regularly send ICMPv6 RS messages to discover the presence of IPv6 routers on the network using the anycast address FF02::2 (all routers)
2. IPv6 routers, upon receiving an ICMPv6 RS message, will send a multicast message on the network in the form of an ICMPv6 Router Advertisement (RA). RA messages are sent to the all multicast nodes address FF02::1.
3. When the attacker wants to establish himself as the IPv6 default router, he sends his own RA message to the all nodes multicast address. The attacker's RA message specifies that his router has the highest preference, taking precedence over all prior legitimate RA messages.
4. The victim, having observed the attacker's RA message with high preference, sends all IPv6 traffic destined for remote networks to the attacker.

## <mark style="color:red;">Exploitation</mark>

The IPv6 router MitM attack is useful in environments where the attacker wants to establish a MitM attack for all the LAN IPv6 victims, forwarding traffic to the legitimate IPv6 router or to the attacker's own IPv6 connection to the internet (possibly tunneled through an IPv4 connection).

#### To implement the IPv6 router MitM attack, the attacker must first configure IPv6 forwarding using the Linux sysctl tool, as shown. Next, the attacker should manually add a default route for use in forwarding traffic from victims to the legitimate router with the Linux "ip route add" command, as shown.

### <mark style="color:orange;">radvd</mark>

Multiple options are available for impersonating an IPv6 router, including the THC-IPV6 tool "fake\_router6."

A more robust approach is to simply configure the Linux IPv6 router software "radvd" (http://www.litech.org/radvd) with the configuration directives specified on this page.

Once the attacker is ready to start the MitM attack, he only needs to start the radvd process, identifying the desired configuration file with the "-C" argument.

```
sysctl -w net.ipv6.conf.all.forwarding=1

# specify the legitimate IPv6 address for the
# cat >/etc/radvd.conf

interface eth0 {
        AdvSendAdvert on;
        # Process should send advertisements
        AdvDefaultPreference high; # Highest advertised preference
        MinRtrAdvInterval 3;
        # 3 second minimum between ads
        MaxRtrAdvInterval 4;
        # 4 second maximum between ads
        prefix fc00:660:0:1::/64 { # Address space and prefix for clients to use
        };
};
^D

radvd -C /etc/radvd.conf
```

### <mark style="color:orange;">fake\_router6</mark>

fake\_router6 is a tool inside THC-IPv6 tools bundled inside Kali linux to test exploit & attack weaknesses & protocol complexity in ipv6 & icmp6 protocols. As the name suggests, it’s from the Hackers’ Choice. Before we begin the attack, lets get under the hood for a minute. In IPv4, you know what is “ARP”, here in IPv6, its replaced by ND expanded as Neighbor Discovery. ND combines the functionality of ARP,ICMP, ICMP-Redirect & router discovery which is present in IPv4. There are several other advanteges & additional functionalities for ND like to discover neighbouring devices & hosts, link the layer2 (link layer) addresses, advertise the presence of a host/router etc. There are basically 5 types of ND messages.

* **Router Solicitation (ICMPv6 type 133)**
* **Router Advertisement (ICMPv6 type 134)**
* **Neighbor Solicitation (ICMPv6 type 135)**
* **Neighbor Advertisement (ICMPv6 type 136)**
* **Redirect (ICMPv6 type 137)**

```
fake_router6 [-HFD] interface network-address/prefix-length [dns-server [router-ip-link-local [mtu [mac-address]]]]

-H adds hop-by-hop
-F fragmentation header
-D dst header
```

#### example:

```
fake_router6 eth0 <replace with your interface> bad::00/64 <replace with your fake n/w>
```

![](<../../../.gitbook/assets/image (280).png>)

![](<../../../.gitbook/assets/image (295) (1).png>)
