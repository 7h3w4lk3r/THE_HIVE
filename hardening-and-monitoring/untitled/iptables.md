# 🔧 IPTables

{% hint style="danger" %}
#### In iptables, Default Policy Rules and the First Matching Rule Wins.

**remember to insert your rule at the beginning of the table or removing mismatching rules from previous line.**
{% endhint %}

## <mark style="color:red;">Kernel Configuration</mark>

#### to see all available network related kernel configurations for IPv4:

```
ls  /proc/sys/net/ipv4/ -l | cut -d " " -f 10
```

Enable broadcast echo Protection

```
echo "1" > /proc/sys/net/ipv4/icmp_echo_ignore_broadcasts
```

Disable Source Routed Packets

```
echo "0" > /proc/sys/net/ipv4/conf/all/accept_source_route
```

Enable TCP SYN Cookie Protection

```
echo 1 > /proc/sys/net/ipv4/tcp_syncookies
```

Disable ICMP Redirect Acceptance

```
echo "0" > /proc/sys/net/ipv4/conf/all/accept_redirects
```

Don't send Redirect Messages

```
echo "0" > /proc/sys/net/ipv4/conf/all/send_redirects
```

Drop Spoofed Packets

```
for f in /proc/sys/net/ipv4/conf/*/rp_filter; do echo "1" > $f; done
```

Log packets with impossible addresses

```
echo "1" > /proc/sys/net/ipv4/conf/all/log_martians
```













