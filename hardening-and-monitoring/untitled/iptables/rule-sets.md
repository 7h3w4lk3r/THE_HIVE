# Rule Sets

{% embed url="https://github.com/trimstray/iptables-essentials" %}

## <mark style="color:red;">Anti DDoS</mark>

```
### 1: Drop invalid packets ### 
/sbin/iptables -t mangle -A PREROUTING -m conntrack --ctstate INVALID -j DROP  

### 2: Drop TCP packets that are new and are not SYN ### 
/sbin/iptables -t mangle -A PREROUTING -p tcp ! --syn -m conntrack --ctstate NEW -j DROP 
 
### 3: Drop SYN packets with suspicious MSS value ### 
/sbin/iptables -t mangle -A PREROUTING -p tcp -m conntrack --ctstate NEW -m tcpmss ! --mss 536:65535 -j DROP  

### 4: Block packets with bogus TCP flags ### 
/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags FIN,SYN FIN,SYN -j DROP
/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags SYN,RST SYN,RST -j DROP
/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags FIN,RST FIN,RST -j DROP
/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags FIN,ACK FIN -j DROP
/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags ACK,URG URG -j DROP
/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags ACK,PSH PSH -j DROP
/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags ALL NONE -j DROP

### 5: Block spoofed packets ### 
/sbin/iptables -t mangle -A PREROUTING -s 224.0.0.0/3 -j DROP 
/sbin/iptables -t mangle -A PREROUTING -s 169.254.0.0/16 -j DROP 
/sbin/iptables -t mangle -A PREROUTING -s 172.16.0.0/12 -j DROP 
/sbin/iptables -t mangle -A PREROUTING -s 192.0.2.0/24 -j DROP 
/sbin/iptables -t mangle -A PREROUTING -s 192.168.0.0/16 -j DROP 
/sbin/iptables -t mangle -A PREROUTING -s 10.0.0.0/8 -j DROP 
/sbin/iptables -t mangle -A PREROUTING -s 0.0.0.0/8 -j DROP 
/sbin/iptables -t mangle -A PREROUTING -s 240.0.0.0/5 -j DROP 
/sbin/iptables -t mangle -A PREROUTING -s 127.0.0.0/8 ! -i lo -j DROP  

### 6: Drop ICMP (you usually don't need this protocol) ### 
/sbin/iptables -t mangle -A PREROUTING -p icmp -j DROP  

### 7: Drop fragments in all chains ### 
/sbin/iptables -t mangle -A PREROUTING -f -j DROP  

### 8: Limit connections per source IP ### 
/sbin/iptables -A INPUT -p tcp -m connlimit --connlimit-above 111 -j REJECT --reject-with tcp-reset  

### 9: Limit RST packets ### 
/sbin/iptables -A INPUT -p tcp --tcp-flags RST RST -m limit --limit 2/s --limit-burst 2 -j ACCEPT 
/sbin/iptables -A INPUT -p tcp --tcp-flags RST RST -j DROP  

### 10: Limit new TCP connections per second per source IP ### 
/sbin/iptables -A INPUT -p tcp -m conntrack --ctstate NEW -m limit --limit 60/s --limit-burst 20 -j ACCEPT 
/sbin/iptables -A INPUT -p tcp -m conntrack --ctstate NEW -j DROP  

### 11: Use SYNPROXY on all ports (disables connection limiting rule) ### 
# Hidden - unlock content above in "Mitigating SYN Floods With SYNPROXY" section
```

## <mark style="color:red;">Common Rules</mark>

```
### default drop policy ###
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT DROP

### # accept anything on localhost ###
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

### # allow traffic once a connection has been made ###
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT


### HTTP / HTTPS ###
iptables -A INPUT -i eth0 -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 443 -m state --state ESTABLISHED -j ACCEPT


### Allowing only SSH to a Network ###
iptables -A OUTPUT -o eth0 -p tcp -d 192.168.100.0/24 --dport 3306 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --sport 3306 -m state --state ESTABLISHED -j ACCEPT


### Allowing the Incoming MySQL port (3306) for TCP Traffic. ###
iptables -A INPUT -i eth0 -p tcp --dport 3306 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 3306 -m state --state ESTABLISHED -j ACCEPT


### Allowing Incoming MySQL Port (3306) for a Specific Network ###
iptables -A INPUT -i eth0 -p tcp -s 192.168.87.0/24 --dport 3306 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 3306 -m state --state ESTABLISHED -j ACCEPT


### Allowing Outgoing MySQL ###
iptables -A OUTPUT -o eth0 -p tcp --dport 3306-m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --sport 3306 -m state --state ESTABLISHED -j ACCEPT


### Allow Sendmail Traffic ###
iptables -A INPUT -i eth0 -p tcp --dport 25 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 25 -m state --state ESTABLISHED -j ACCEPT


### Allowing IMAP & POP3 Ports ###
iptables -A INPUT -i eth0 -p tcp --dport 143 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 143 -m state --state ESTABLISHED -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --dport 110 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 110 -m state --state ESTABLISHED -j ACCEPT


### Forward a Port to 5722 to 22(SSH) ###
iptables -t nat -A PREROUTING -p tcp -d 192.168.87.100 --dport 5722 -j DNAT --to 192.168.87.200:22


### Allowing Port 873 (rsync) for Backups ###
iptables -A INPUT -i eth0 -p tcp -s 192.168.87.0/24 --dport 873 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 873 -m state --state ESTABLISHED -j ACCEPT


### Blocking an IP address ###
BLOCK_ADDRESS="192.168.87.100"
iptables -A INPUT -s "$BLOCK_ADDRESS" -j DROP
iptables -A INPUT -i eth0 -s “$ BLOCK_ADDRESS ” -j DROP
iptables -A INPUT -i eth0 -p tcp -s “$ BLOCK_ADDRESS ” -j DROP





```





















