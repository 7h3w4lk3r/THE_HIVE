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



















