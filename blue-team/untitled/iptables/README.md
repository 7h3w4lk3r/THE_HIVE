# ⭕ iptables

{% hint style="danger" %}
**Be careful with iptables rules, you might lock yourself out of remote machines.**

**In iptables, Default Policy Rules and the First Matching Rule Wins.**

**Remember to insert your rule at the beginning of the table or removing mismatching rules from previous line.**
{% endhint %}

{% embed url="https://www.andreafortuna.org/2019/05/08/iptables-a-simple-cheatsheet" %}

{% embed url="https://sbcode.net/zabbix/iptables-cheatsheet" %}

{% embed url="https://www.unix-ninja.com/p/An_iptables_cheat-sheet" %}

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

![](<../../../.gitbook/assets/image (294).png>)

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

The previous two lines do not allow other computers to route ping messages through your router, because it only handles INPUT and OUTPUT.

For routing of ping, you will need to enable it on the FORWARD chain. The following command enables routing of icmp messages between networks.

```
iptables -A FORWARD -p icmp --icmp-type any -j ACCEPT
```

## <mark style="color:red;">Allow Established & Outbound Connections</mark>

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

<mark style="color:green;">http/https</mark>

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

## <mark style="color:red;">Logging</mark>

#### <mark style="color:orange;">logging rules can apply anywhere in the table.</mark>

{% hint style="danger" %}
**Don't put logging rules at the top of your table as the first rule, otherwise iptables will log EVERYTHING and will lead to a DoS condition if you dont have enough resource to store that data.**

**A best practice is to add logging rules at the end of the table as the last rule so anything that is not filtered by other rules will be logged.**
{% endhint %}

{% hint style="info" %}
**Remember that anything you drop before the log rules will not be logged.**
{% endhint %}

#### add these at the end:

```
iptables -A INPUT -j LOG
iptables -A OUTPUT -j LOG
iptables -A FORWARD -j LOG
```

## <mark style="color:red;">Rate Limiting</mark>

Protect against DDoS

### <mark style="color:orange;">Anti-DDoS Kernel Settings (sysctl.conf)</mark>

nano /etc/sysctl.conf

```
kernel.printk = 4 4 1 7 
kernel.panic = 10 
kernel.sysrq = 0 
kernel.shmmax = 4294967296 
kernel.shmall = 4194304 
kernel.core_uses_pid = 1 
kernel.msgmnb = 65536 
kernel.msgmax = 65536 
vm.swappiness = 20 
vm.dirty_ratio = 80 
vm.dirty_background_ratio = 5 
fs.file-max = 2097152 
net.core.netdev_max_backlog = 262144 
net.core.rmem_default = 31457280 
net.core.rmem_max = 67108864 
net.core.wmem_default = 31457280 
net.core.wmem_max = 67108864 
net.core.somaxconn = 65535 
net.core.optmem_max = 25165824 
net.ipv4.neigh.default.gc_thresh1 = 4096 
net.ipv4.neigh.default.gc_thresh2 = 8192 
net.ipv4.neigh.default.gc_thresh3 = 16384 
net.ipv4.neigh.default.gc_interval = 5 
net.ipv4.neigh.default.gc_stale_time = 120 
net.netfilter.nf_conntrack_max = 10000000 
net.netfilter.nf_conntrack_tcp_loose = 0 
net.netfilter.nf_conntrack_tcp_timeout_established = 1800 
net.netfilter.nf_conntrack_tcp_timeout_close = 10 
net.netfilter.nf_conntrack_tcp_timeout_close_wait = 10 
net.netfilter.nf_conntrack_tcp_timeout_fin_wait = 20 
net.netfilter.nf_conntrack_tcp_timeout_last_ack = 20 
net.netfilter.nf_conntrack_tcp_timeout_syn_recv = 20 
net.netfilter.nf_conntrack_tcp_timeout_syn_sent = 20 
net.netfilter.nf_conntrack_tcp_timeout_time_wait = 10 
net.ipv4.tcp_slow_start_after_idle = 0 
net.ipv4.ip_local_port_range = 1024 65000 
net.ipv4.ip_no_pmtu_disc = 1 
net.ipv4.route.flush = 1 
net.ipv4.route.max_size = 8048576 
net.ipv4.icmp_echo_ignore_broadcasts = 1 
net.ipv4.icmp_ignore_bogus_error_responses = 1 
net.ipv4.tcp_congestion_control = htcp 
net.ipv4.tcp_mem = 65536 131072 262144 
net.ipv4.udp_mem = 65536 131072 262144 
net.ipv4.tcp_rmem = 4096 87380 33554432 
net.ipv4.udp_rmem_min = 16384 
net.ipv4.tcp_wmem = 4096 87380 33554432 
net.ipv4.udp_wmem_min = 16384 
net.ipv4.tcp_max_tw_buckets = 1440000 
net.ipv4.tcp_tw_recycle = 0 
net.ipv4.tcp_tw_reuse = 1 
net.ipv4.tcp_max_orphans = 400000 
net.ipv4.tcp_window_scaling = 1 
net.ipv4.tcp_rfc1337 = 1 
net.ipv4.tcp_syncookies = 1 
net.ipv4.tcp_synack_retries = 1 
net.ipv4.tcp_syn_retries = 2 
net.ipv4.tcp_max_syn_backlog = 16384 
net.ipv4.tcp_timestamps = 1 
net.ipv4.tcp_sack = 1 
net.ipv4.tcp_fack = 1 
net.ipv4.tcp_ecn = 2 
net.ipv4.tcp_fin_timeout = 10 
net.ipv4.tcp_keepalive_time = 600 
net.ipv4.tcp_keepalive_intvl = 60 
net.ipv4.tcp_keepalive_probes = 10 
net.ipv4.tcp_no_metrics_save = 1 
net.ipv4.ip_forward = 0 
net.ipv4.conf.all.accept_redirects = 0 
net.ipv4.conf.all.send_redirects = 0 
net.ipv4.conf.all.accept_source_route = 0 
net.ipv4.conf.all.rp_filter = 1
```

These sysctl.conf settings help to maximize the performance of your server under DDoS as well as the effectiveness of the iptables rules.

### <mark style="color:orange;">IPtables Anti-DDoS Rules</mark>

#### Block New Packets That Are Not SYN:

```
iptables -t mangle -A PREROUTING -p tcp ! --syn -m conntrack --ctstate NEW -j DROP
```

This blocks all packets that are new (don’t belong to an established connection) and don’t use the SYN flag. This rule is similar to the “Block Invalid Packets” one, but we found that it catches some packets that the other one doesn’t.

#### Block Uncommon MSS Values:

```
iptables -t mangle -A PREROUTING -p tcp -m conntrack --ctstate NEW -m tcpmss ! --mss 536:65535 -j DROP
```

The above iptables rule blocks new packets (only SYN packets can be new packets as per the two previous rules) that use a TCP MSS value that is not common. This helps to block dumb SYN floods.

#### Block Packets With Bogus TCP Flags:

```
iptables -t mangle -A PREROUTING -p tcp --tcp-flags FIN,SYN FIN,SYN -j DROP
iptables -t mangle -A PREROUTING -p tcp --tcp-flags SYN,RST SYN,RST -j DROP
iptables -t mangle -A PREROUTING -p tcp --tcp-flags FIN,RST FIN,RST -j DROP
iptables -t mangle -A PREROUTING -p tcp --tcp-flags FIN,ACK FIN -j DROP
iptables -t mangle -A PREROUTING -p tcp --tcp-flags ACK,URG URG -j DROP
iptables -t mangle -A PREROUTING -p tcp --tcp-flags ACK,PSH PSH -j DROP
iptables -t mangle -A PREROUTING -p tcp --tcp-flags ALL NONE -j DROP
```

The above ruleset blocks packets that use bogus TCP flags, ie. TCP flags that legitimate packets wouldn’t use.

#### Block Packets From Private Subnets (Spoofing):

```
iptables -t mangle -A PREROUTING -s 224.0.0.0/3 -j DROP 
iptables -t mangle -A PREROUTING -s 169.254.0.0/16 -j DROP 
iptables -t mangle -A PREROUTING -s 172.16.0.0/12 -j DROP 
iptables -t mangle -A PREROUTING -s 192.0.2.0/24 -j DROP 
iptables -t mangle -A PREROUTING -s 192.168.0.0/16 -j DROP 
iptables -t mangle -A PREROUTING -s 10.0.0.0/8 -j DROP 
iptables -t mangle -A PREROUTING -s 0.0.0.0/8 -j DROP 
iptables -t mangle -A PREROUTING -s 240.0.0.0/5 -j DROP 
iptables -t mangle -A PREROUTING -s 127.0.0.0/8 ! -i lo -j DROP
```

These rules block spoofed packets originating from private (local) subnets. On your public network interface you usually don’t want to receive packets from private source IPs.

These rules assume that your loopback interface uses the 127.0.0.0/8 IP space.

These five sets of rules alone already block many TCP-based DDoS attacks at very high packet rates.

With the kernel settings and rules mentioned above, you’ll be able to filter ACK and SYN-ACK attacks at line rate.

#### mitigate Ping of Death (ping flood):

```
iptables -t mangle -A PREROUTING -p icmp -j DROP
```

#### TCP connection attacks:

```
iptables -A INPUT -p tcp -m connlimit --connlimit-above 80 -j REJECT --reject-with tcp-reset
```

It rejects connections from hosts that have more than 80 established connections. If you face any issues you should raise the limit as this could cause troubles with legitimate clients that establish a large number of TCP connections.

#### Limit the new TCP connections that a client can establish per second:

```
iptables -A INPUT -p tcp -m conntrack --ctstate NEW -m limit --limit 60/s --limit-burst 20 -j ACCEPT 
iptables -A INPUT -p tcp -m conntrack --ctstate NEW -j DROP
```

This can be useful against connection attacks, but not so much against SYN floods because the usually use an endless amount of different spoofed source IPs.

#### block fragmented packets:

```
iptables -t mangle -A PREROUTING -f -j DROP
```

Normally you don’t need those and blocking fragments will mitigate UDP fragmentation flood. But most of the time UDP fragmentation floods use a high amount of bandwidth that is likely to exhaust the capacity of your network card, which makes this rule optional and probably not the most useful one.

#### limits incoming TCP RST packets to mitigate TCP RST floods:

```
iptables -A INPUT -p tcp --tcp-flags RST RST -m limit --limit 2/s --limit-burst 2 -j ACCEPT 
iptables -A INPUT -p tcp --tcp-flags RST RST -j DROP
```

### <mark style="color:orange;">Mitigating SYN Floods With SYNPROXY</mark>

SYNPROXY is a new target of iptables that has been added in Linux kernel version 3.12 and iptables 1.4.21. CentOS 7 backported the feature and it’s available in its 3.10 default kernel.

The purpose of SYNPROXY is to check whether the host that sent the SYN packet actually establishes a full TCP connection or just does nothing after it sent the SYN packet.

If it does nothing, it discards the packet with minimal performance impact.

Here are iptables SYNPROXY rules that help mitigate SYN floods that bypass our other rules:

```
iptables -t raw -A PREROUTING -p tcp -m tcp --syn -j CT --notrack 
iptables -A INPUT -p tcp -m tcp -m conntrack --ctstate INVALID,UNTRACKED -j SYNPROXY --sack-perm --timestamp --wscale 7 --mss 1460 
iptables -A INPUT -m conntrack --ctstate INVALID -j DROP
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
