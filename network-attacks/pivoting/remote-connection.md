# 🔧 VPN Tunneling

## Creating New Interface

{% hint style="warning" %}
You need root in both devices (as you are going to create new interfaces) and the sshd config has to allow root login.
{% endhint %}

create an interface on both devices:

```
#Client side VPN IP
ip addr add 1.1.1.2/32 peer 1.1.1.1 dev tun0 

#Server side VPN IP
ip addr add 1.1.1.1/32 peer 1.1.1.2 dev tun0 
```

Enable forwarding in Server side:

```
echo 1 > /proc/sys/net/ipv4/ip_forward
iptables -t nat -A POSTROUTING -s 1.1.1.2 -o eth0 -j MASQUERADE
```

Set new route on client side:

```
route add -net 10.0.0.0/16 gw 1.1.1.1
```

## NETSH (Windows)

{% hint style="warning" %}
for this to work, the Windows system must have the IP Helper service running and IPv6 support must be enabled for the interface we want to use. Fortunately, both are on and enabled by default on Windows operating systems.
{% endhint %}

```
netsh interface portproxy add v4tov4 listenport=4455 listenaddress=10.11.0.22 connectport=445 connectaddress=192.168.1.110
netsh advfirewall firewall add rule name="forward_port_rule" protocol=TCP dir=in localip=10.11.0.22 localport=4455 action=allow
```

test:

```
smbclient -L 10.11.0.22 --port=4455 --user=Administrator
```
