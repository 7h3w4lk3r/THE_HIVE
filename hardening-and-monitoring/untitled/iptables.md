# iptables

{% embed url="https://www.andreafortuna.org/2019/05/08/iptables-a-simple-cheatsheet" %}

{% embed url="https://sbcode.net/zabbix/iptables-cheatsheet" %}

{% embed url="https://www.unix-ninja.com/p/An_iptables_cheat-sheet" %}

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



## <mark style="color:red;">Command Options</mark>

Tables options:

```
-t >>> set a table to operate opon (for firewalls often ‘filter’, for NAT config is ‘nat’)
-L >>> show config for a chain or all chains without name specification
-S >>> show rulses for a chain or all chains
-F >>> flush rules for a chain or all chains
-P [chain] [target] >>> set the default policy for a chain
-A [cahin]>>> specify a chain
-D [cahin] [rule] >>> delete a rule form a chain (by number or rule specification)
-I [chain] [num] [rule] >>> add new rule to a chain
-R [chain] [num] [rule] >>> replace an existing rule in a chain
```

default policies:

```
iptables -P [INPUT/OUTPUT/FORWARD] [DROP/REJECT/ACCEPT]
```

Matching criteria:

```
-m [name] >>> adds extended matching rules provided by module name
-p [protocol] >>> tcp, udp, udplite, icmp, esp, shtp, all
--sport >>> specify source port or 1:65535 for a port range
--dport >>> specify destination port or 1:65535 for a port range
-s >>> source address or 192.168.1.0/24  for a net block 
-d >>> destiation address or 192.168.1.0/24  for a net block 
-i >>> input interface only
-o >>> output interface only
--state >>> connection state: INVALID, NEW, ESTABLISHED, RELATED
-j [target] >>> action: ACCEPT, DROP, REJECT
-g [chain] >>> continue the process in another chain

-nL >>> show numeric IPs and ports 
-nvL >>> more verbose output
```

## <mark style="color:red;">Tables</mark>

By default there are three tables in the kernel that contain sets of rules.

1. The filter table is used for packet filtering
2. The nat table is used for address translation
3. The mangle table can be used for special-purpose processing of packets

## <mark style="color:red;">Filter Table</mark>

Packet filtering is a bit more than packet forwarding. While packet forwarding uses only a routing table to make decisions, packet filtering also uses a list of rules. The kernel will inspect packets and decide based on these rules what to do with each packet.

The filter table in iptables has three chains (sets of rules). The INPUT chain is used for any packet coming into the system. The OUTPUT chain is for any packet leaving the system. And the FORWARD chain is for packets that are forwarded (routed) through the system.

![](<../../.gitbook/assets/image (294).png>)

## <mark style="color:red;">Setting Default Policy</mark>

**The most secure firewall policy would be to DROP everything and then specifically accept some traffic**. A package that is dropped will not continue in any chain, and no warning or error will be sent anywhere.

{% hint style="danger" %}
**The below commands lock down a computer. Do not execute these commands inside a remote SSH shell.**
{% endhint %}

Remove any existing rules from all chains

```
iptables  --flush
```

Specific tables can then be flushed using the -t

option:

```
iptables -t nat --flush
iptables -t mangle --flush
```

delete any user-defined chains.

```
iptables -X
iptables -t nat -X
iptables -t mangle -X
```

Resetting Default Policies and Stopping the Firewall:

```
ipbles -P INPUT DROP
iptables -P OUTPUT DROP
iptables -P FORWARD DROP
```

## <mark style="color:red;">Allow Loopback Access</mark>

```
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# see the results
iptables -n -L -v --line-number
```

## <mark style="color:red;">Allow ICMP echo (ping)</mark>

ICMP types:

```
use --icmp-type 8 for echo request only
type 0 → echo reply
type 3 → destination unreachable messages
type 5 → redirect messages
type 8 → echo request
type 11 → time exceeded messages
type 12 → parameter problem messages
```

```
iptables -A INPUT -p icmp --icmp-type any -j ACCEPT
iptables -A OUTPUT -p icmp --icmp-type any -j ACCEPT
```

### <mark style="color:orange;">Route Pings</mark>

The previous two lines do not allow other computers to route ping messages through your router, because it only handles INPUT and OUTPUT.&#x20;

For routing of ping, you will need to enable it on the FORWARD chain. The following command enables routing of icmp messages between networks.

```
iptables -A FORWARD -p icmp --icmp-type any -j ACCEPT
```

## <mark style="color:red;">Allow Established & Outbound Connections</mark>&#x20;

The following commands will implement a policy to allow all outbound connections and all established TCP, UDP and ICMP connections:

```
# iptables -A OUTPUT -p tcp -m state --state NEW,ESTABLISHED -j ACCEPT
# iptables -A OUTPUT -p udp -m state --state NEW,ESTABLISHED -j ACCEPT
# iptables -A OUTPUT -p icmp -m state --state NEW,ESTABLISHED -j ACCEPT
# iptables -A INPUT -p tcp -m state --state ESTABLISHED,RELATED -j ACCEPT
# iptables -A INPUT -p udp -m state --state ESTABLISHED,RELATED -j ACCEPT
# iptables -A INPUT -p icmp -m state --state ESTABLISHED,RELATED -j ACCEPT
```

{% hint style="info" %}
Using the state module alone, INVALID will break protocols that use bi-directional connections or multiple connections or exchanges, unless an ALG is provided for the protocol.
{% endhint %}

## <mark style="color:red;">Refuse spoofed Packets</mark>

Refuse spoofed packets pretending to be from the external interface's IP address.

```
iptables -A INPUT -i $INTERNET -s $IPADDR -j DROP
```

Refuse packets claiming to be from a Class A private network.

```
iptables -A INPUT -i $INTERNET -s $CLASS_A -j DROP
```

Refuse packets claiming to be from the loopback interface.

```
iptables  -A INPUT -i $INTERNET -s $LOOPBACK -j DROP
```

Refuse malformed broadcast packets

```
iptables  -A INPUT -i $INTERNET -s $BROADCAST_DEST -j LOG
iptables -A INPUT -i $INTERNET -s $BROADCAST_DEST -j DROP
iptables -A INPUT -i $INTERNET -d $BROADCAST_SRC -j LOG
iptables -A INPUT -i $INTERNET -d $BROADCAST_SRC -j DROP
```

Refuse directed broadcasts (Used to map networks and in Denial of Service attacks)

```
iptables  -A INPUT -i $INTERNET -d $SUBNET_BASE -j DROP
iptables  -A INPUT -i $INTERNET -d $SUBNET_BROADCAST -j DROP
```

Refuse limited broadcasts

```
iptables  -A INPUT -i $INTERNET -d $BROADCAST_DEST -j DROP
```

## <mark style="color:red;">Custom Rules</mark>

### <mark style="color:orange;">Inserting and replacing rules</mark>

```
iptables -I/-R INPUT [line number] -s 59.45.175.10 -j ACCEPT

iptables -I INPUT 1 -s 59.45.175.10 -j ACCEPT
iptables -R INPUT 1 -s 59.45.175.10 -j ACCEPT
```

### <mark style="color:orange;">blocking an IP</mark>

```
iptables -A INPUT -s IP-Address -j DROP

# works without ip tables
ip route add prohibit 192.168.30.83/32  
```

### <mark style="color:orange;">allow a specific IP</mark>

```
iptables -I INPUT -p tcp -s XXX.XXX.XXX.XXX -j ACCEPT
iptables -I OUTPUT -p tcp -d  XXX.XXX.XXX.XXX -j ACCEPT`
```

### <mark style="color:orange;">deleting a rule</mark>

```
iptables -D [chain name] [rule line number]
iptables -D INPUT 2
```

### <mark style="color:orange;">add connection tracking rules</mark>

```
iptables -A INPUT -j ACCEPT -m conntrack --ctstate ESTABLISHED,RELATED

iptables -A OUTPUT -j ACCEPT -m conntrack --ctstate ESTABLISHED,RELATED
```

### <mark style="color:orange;">Allowing a specific port</mark>

<mark style="color:green;">established and related connections</mark>

```
iptables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```

<mark style="color:green;">ssh</mark>

```
iptables -A INPUT -i eth0 -p tcp --dport 22 -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 22 -j ACCEPT
```

<mark style="color:green;">dns</mark>

```
iptables -A INPUT -p udp --sport 53 -j ACCEPT
iptables -A OUTPUT -p udp --dport 53 -j ACCEPT
iptables -A INPUT -p tcp --sport 53 -j ACCEPT
iptables -A OUTPUT -p tcp --dport 53 -j ACCEPT
```

&#x20;<mark style="color:green;">http/https</mark>

```
iptables -A INPUT -p tcp --sport 443 -j ACCEPT
iptables -A OUTPUT -p tcp --dport 443 -j ACCEPT
iptables -A INPUT -p tcp --sport 80 -j ACCEPT
iptables -A OUTPUT -p tcp --dport 80 -j ACCEPT
```

### <mark style="color:orange;">Allowing access from a subnet</mark>

```
iptables -A INPUT -i eth1 -s 10.1.1.0/24 -p tcp -j ACCEPT
iptables -A OUTPUT -o eth1 -d 10.1.1.0/24 -p tcp -j ACCEPT
```

## <mark style="color:red;">Rate Limiting</mark>

useful blocking bruteforce.

ipables rules are applied in order from top to buttom of the rules list.

![](<../../.gitbook/assets/image (277).png>)

here out ssh accept rule input chain is number 8 in the list so to apply our rate limmiting rule we have to insert it above rule number 8 to performe it before that. we ise -I for insert instead if -A for append

```
iptables -I INPUT 7 -p tcp --dport 22 -m conntrack NEW -m comment --comment ""
```

### <mark style="color:orange;">Per-IP packet limits</mark>

```
iptables -A INPUT -p tcp -m tcp --dport 22 -m conntrack --ctstate NEW -m recent --set --name SSHLIMIT --rsource
iptables -A INPUT -p tcp -m tcp --dport 22 -m conntrack --ctstate NEW -m recent --set --name SSHLIMIT --update --seconds 180 --hitcount 5 --name SSH --rsource -j DROP
```

## <mark style="color:red;">Custom Chains</mark>

```
/iptables -N ssh-rules
iptables -A ssh-rules -s 18.130.0.0/16 -j ACCEPT
iptables -A ssh-rules -s 18.11.0.0/16 -j ACCEPT
iptables -A ssh-rules -j DROP
iptables -A INPUT -p tcp -m tcp --dport 22 -j ssh-rules
iptables -X ssh-rules
```

## <mark style="color:red;">save/restore/persistent</mark>

Use iptables save to automatically implement these rules when the firewall is (re)started.

```
/etc/init.d/iptables save
or
iptables-save
```

```
iptables-save -f [output file]
iptables-restore [rules file]
```

make persistence after reboot:

```
sudo apt-get install iptables-persistent
```

```
iptables-save >/etc/iptables/rules.v4
ip6tables-save >/etc/iptables/rules.v6

iptables-save > /etc/iptables.conf
ip6tables-save > /etc/ip6tables.conf

pre-up iptables-restore < /etc/iptables.conf
pre-up ip6tables-restore < /etc/ip6tables.conf
```

## <mark style="color:red;">adding a comment</mark>

```
-m comment --comment 'comment text'
```

example:

```
iptables -A INPUT -j ACCEPT -p tcp --dport 22 -m comment --comment 'ssh port 22'
```

## <mark style="color:red;">NAT Masquerading</mark>

```
iptables -A FORWARD -m comment --comment "established traffic" -j ACCEPT -m conntrack --ctstate ESTABLISHED,RELATED

iptables -A FORWARD -j ACCEPT -i lan -o wan -m comment --comment "allow outbound traffic from lan"

iptables -t nat -L

iptables -t nat -A POSTROUTING -o wan -j MASQUERADE -m comment --comment "masquerade lan to wan" 
```

apply ip forwarding for NAT:

```
echo 1 > /proc/sys/net/ipv4/ip_forward
```

## <mark style="color:red;">port address translation</mark>

allow to reach a service over a firewall

```
iptables -t nat -A PREROUTING -p tcp --dport 22 -m comment --commnet "ssh PAT" -d [target ip] -j DNAT --to-destination [dest ip] 

iptabes -A FORWARD -p tcp --dport 22 -d [dest ip] -j ACCEPT -m comment "ssh into dest"
```











