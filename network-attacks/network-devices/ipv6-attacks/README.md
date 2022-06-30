# ⭕ IPv6 Attacks

## <mark style="color:red;">Linux IPv6 Interface Configuration</mark>

```
# Load the Linux IPv6 kernel module
modprobe ipv6
miredo

#Add an IPv6 address to eth0 with a 64-bit mask
ifconfig eth0 inet6 add fc00:660:0:1::2/64

# Remove an IPv6 address 
ifconfig eth0 inet6 del fc00:660:0:1::2/64

# Display configured IPv6 addresses
ifconfig eth0 | inet6
```

## <mark style="color:red;">IPv6 Enumeration</mark>

```
miredo  # run before pinging a target
ping6 -c 5 ff02::1%eth0 >/dev/null

# show neighbors
ip -6 neigh

# scan ipv6
nmap -6 -sS -sC fc00:660:0:1::23

# discover ipv6
dig +short IN AAAA google.com
```

Free IPv6 tunneling services from SixXS (http://www.sixxs.net) and Hurricane Electric (http://www.he.net) offer IPv4-to-IPv6 tunneled access with a static IPv6 address. Configuring a Linux host to create a tunnel with SixXS or Hurricane Electric starts with creating an account on the respective provider's website and then configuring your Linux host to establish a tunnel. A step-by-step guide for configuring Ubuntu Linux (including Kali Linux) for SixXS or Hurricane Electric tunneling is available at https://wiki.ubuntu.com/IPv6.

A simpler IPv6 tunnel option for Linux systems is to use the IPv6 Teredo tunneling protocol with the "miredo" daemon. Simply **starting the miredo process** on most Linux distributions **will be sufficient to establish an IPv6 connection**, **allowing you to ping IPv6 hosts** such as ipv6.google.com. Once the Teredo tunnel is established, other scanning tools such as Nmap will also allow you to scan and enumerate remote IPv6 targets.
