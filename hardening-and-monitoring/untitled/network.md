# Network

## <mark style="color:red;">Disable Uncommon Protocols</mark>

#### Disable these protocols:

### <mark style="color:orange;">DCCP</mark>

Edit or create a file in the /etc/modprobe.d/ directory ending in .conf Example: vim /etc/modprobe.d/dccp.conf and add the following line:

```
install dccp /bin/true
```

### <mark style="color:orange;">SCTP</mark>

Edit or create a file in the /etc/modprobe.d/ directory ending in .conf Example: vim /etc/modprobe.d/sctp.conf and add the following line:

```
install sctp /bin/true
```

### <mark style="color:orange;">RDS</mark>

Edit or create a file in the /etc/modprobe.d/ directory ending in .conf Example: vim /etc/modprobe.d/rds.conf and add the following line:

```
install rds /bin/true
```

### <mark style="color:orange;">TIPC</mark>

Edit or create a file in the /etc/modprobe.d/ directory ending in .conf Example: vim /etc/modprobe.d/tipc.conf and add the following line:

```
install tipc /bin/true
```

## <mark style="color:red;">Disable IP forwarding</mark>

#### ensures that a system with multiple interfaces (for example, a hard proxy), will never be able to forward packets, and therefore, never serve as a router.

Set the following parameter in /etc/sysctl.conf or a /etc/sysctl.d/\* file:

```
net.ipv4.ip_forward = 0
net.ipv6.conf.all.forwarding = 0
```

Run the following commands to set the active kernel parameters:

```
# sysctl -w net.ipv4.ip_forward=0
# sysctl -w net.ipv6.conf.all.forwarding=0
# sysctl -w net.ipv4.route.flush=1
# sysctl -w net.ipv6.route.flush=1
```

## <mark style="color:red;">Disable Packet Redirection</mark>

#### ICMP Redirects are used to send routing information to other hosts. As a host itself does not act as a router (in a host only configuration), there is no need to send redirects.

Set the following parameters in /etc/sysctl.conf or a /etc/sysctl.d/\* file:

```
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
```

Run the following commands to set the active kernel parameters:

```
# sysctl -w net.ipv4.conf.all.send_redirects=0
# sysctl -w net.ipv4.conf.default.send_redirects=0
# sysctl -w net.ipv4.route.flush=1
```

## <mark style="color:red;">Drop Source Routed Packets</mark>

#### Under normal routing circumstances, an attacker from the Internet routable addresses could not use the system as a way to reach the private address systems. If, however, source routed packets were allowed, they could be used to gain access to the private address systems as the route could be specified, rather than rely on routing protocols that did not allow this routing.

{% hint style="info" %}
Today, network devices such as firewalls, routers and switches will drop these packets by default before it even reaches your system, but its recommended to set these rules just to be on the safe side.
{% endhint %}

Set the following parameters in /etc/sysctl.conf or a /etc/sysctl.d/\* file:

```
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0
net.ipv6.conf.all.accept_source_route = 0
net.ipv6.conf.default.accept_source_route = 0
```

Run the following commands to set the active kernel parameters:

```
# sysctl -w net.ipv4.conf.all.accept_source_route=0
# sysctl -w net.ipv4.conf.default.accept_source_route=0
# sysctl -w net.ipv6.conf.all.accept_source_route=0
# sysctl -w net.ipv6.conf.default.accept_source_route=0
# sysctl -w net.ipv4.route.flush=1
# sysctl -w net.ipv6.route.flush=1
```

## <mark style="color:red;">Drop ICMP Redirects</mark>

#### Attackers could use bogus ICMP redirect messages to maliciously alter the system routing tables and get them to send packets to incorrect networks and allow your system packets to be captured.

Set the following parameters in /etc/sysctl.conf or a /etc/sysctl.d/\* file:

```
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
net.ipv6.conf.default.accept_redirects = 0
```

Run the following commands to set the active kernel parameters:

```
# sysctl -w net.ipv4.conf.all.accept_redirects=0
# sysctl -w net.ipv4.conf.default.accept_redirects=0
# sysctl -w net.ipv6.conf.all.accept_redirects=0
# sysctl -w net.ipv6.conf.default.accept_redirects=0
# sysctl -w net.ipv4.route.flush=1
# sysctl -w net.ipv6.route.flush=1
```

## <mark style="color:red;">Log Suspicious Packets</mark>

Set the following parameters in /etc/sysctl.conf or a /etc/sysctl.d/\* file:

```
net.ipv4.conf.all.log_martians = 1
net.ipv4.conf.default.log_martians = 1
```

Run the following commands to set the active kernel parameters:

```
# sysctl -w net.ipv4.conf.all.log_martians=1
# sysctl -w net.ipv4.conf.default.log_martians=1
# sysctl -w net.ipv4.route.flush=1
```

## <mark style="color:red;">Ignore Broadcast ICMP</mark>

#### Accepting ICMP echo and timestamp requests with broadcast or multicast destinations for your network could be used to trick your host into starting (or participating) in a Smurf attack. A Smurf attack relies on an attacker sending large amounts of ICMP broadcast messages with a spoofed source address.

Set the following parameters in /etc/sysctl.conf or a /etc/sysctl.d/\* file:

```
net.ipv4.icmp_echo_ignore_broadcasts = 1
```

Run the following commands to set the active kernel parameters:

```
# sysctl -w net.ipv4.icmp_echo_ignore_broadcasts=1
# sysctl -w net.ipv4.route.flush=1
```

## <mark style="color:red;">Ignore Bogus ICMP Responses</mark>

#### Some routers (and some attackers) will send responses that violate RFC-1122 and attempt to fill up a log file system with many useless error messages.

Set the following parameter in /etc/sysctl.conf or a /etc/sysctl.d/\* file:

```
net.ipv4.icmp_ignore_bogus_error_responses = 1
```

Run the following commands to set the active kernel parameters:

```
# sysctl -w net.ipv4.icmp_ignore_bogus_error_responses=1
# sysctl -w net.ipv4.route.flush=1
```

## <mark style="color:red;">Enable Reverse Path Filtering</mark>

#### Setting these flags is a good way to deter attackers from sending your system bogus packets that cannot be responded to. One instance where this feature breaks down is if asymmetrical routing is employed. This would occur when using dynamic routing protocols (bgp, ospf, etc) on your system.

Set the following parameters in /etc/sysctl.conf or a /etc/sysctl.d/\* file:

```
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1
```

Run the following commands to set the active kernel parameters:

```
# sysctl -w net.ipv4.conf.all.rp_filter=1
# sysctl -w net.ipv4.conf.default.rp_filter=1
# sysctl -w net.ipv4.route.flush=1
```

## <mark style="color:red;">Enable TCP SYN Cookies</mark>

#### Attackers use SYN flood attacks to perform a denial of service attacked on a system by sending many SYN packets without completing the three way handshake. This will quickly use up slots in the kernel's half-open connection queue and prevent legitimate connections from succeeding. SYN cookies allow the system to keep accepting valid connections, even if under a denial of service attack

Set the following parameters in /etc/sysctl.conf or a /etc/sysctl.d/\* file:

```
net.ipv4.tcp_syncookies = 1
```

Run the following commands to set the active kernel parameters:

```
# sysctl -w net.ipv4.tcp_syncookies=1
# sysctl -w net.ipv4.route.flush=1
```

## <mark style="color:red;">Drop IPv6 Router Advertisements</mark>

#### It is recommended that systems do not accept router advertisements as they could be tricked into routing traffic to compromised machines. Setting hard routes within the system (usually a single default route to a trusted router) protects the system from bad routes.

Set the following parameters in /etc/sysctl.conf or a /etc/sysctl.d/\* file:

```
net.ipv6.conf.all.accept_ra = 0
net.ipv6.conf.default.accept_ra = 0
```

Run the following commands to set the active kernel parameters:

```
# sysctl -w net.ipv6.conf.all.accept_ra=0
# sysctl -w net.ipv6.conf.default.accept_ra=0
# sysctl -w net.ipv6.route.flush=1
```

## <mark style="color:red;">TCP Wrappers</mark>

{% embed url="https://www.linuxsecrets.com/3311-linux-wrapper-explained" %}

{% embed url="https://www.tecmint.com/secure-linux-tcp-wrappers-hosts-allow-deny-restrict-access" %}

TCP wrappers provide basic traffic filtering of incoming network traffic. Access to “wrapped” network services running on a Linux server from other systems can be allowed or denied. A TCP wrapped service is one that has been compiled against the `libwrap.a` library. Use the ldd command to determine whether a network service is linked to `libwrap.a`.

### <mark style="color:orange;">Installation</mark>

#### RedHat Distributions

`yum install tcp_wrappers`

#### Debian Based Systems

`apt-get install tcpd`

#### Suse Linux Based Operating Systems

`zypper in tcp_wrappers`

### <mark style="color:orange;">Configuration files</mark>

#### TCP wrappers rely on two configuration files as the basis for access control:

* **/etc/hosts.allow**
* **/etc/hosts.deny**

When a client attempts to connect to a network service on a remote system, these files are used to determine whether client access is allowed or denied. Use `/etc/hosts.allow` and `/etc/hosts.deny` to define rules that selectively allow or deny clients access to server daemons on local system. The format for entries is as follows for both files:

```
daemon_list : client_list [: command]
```

A description of each field follows:

* **daemon\_list**: A comma-separated list of daemons, or keyword ALL for all daemons
* **client\_list**: A comma-separated list of clients, or keyword ALL for all clients
* **command**: An optional command that is executed when a client tries to access a server daemon

To allow client access, add the client host name or IP address in `/etc/hosts.allow`. To deny client access, add its name or IP address in `/etc/hosts.deny`.

The `/etc/hosts.allow` file is read first and is read from top to bottom. If a daemon-client pair matches the first line in the file, access is granted. If the line is not a match, the next line is read and the same check is performed. If all lines are read and no match occurs, the `/etc/hosts.deny` file is read, starting at the top. If a daemon-client pair match is found in the deny file, access is denied. If no rules for the daemon-client pair are found in either file, or if neither file exists, access to the service is granted.

Because access rules in hosts.allow are applied first, they take precedence over rules specified in hosts.deny. Therefore, if access to a service is allowed in hosts.allow, a rule denying access to that same service in hosts.deny is ignored. The following are some examples of entries in the /etc/hosts.allow file:

### <mark style="color:orange;">Examples</mark>

To allow clients on the 192.168.2 subnet to access FTP (daemon is vsftpd):

```
# vi /etc/hosts.allow
vsftpd : 192.168.2.*
```

To allow all clients to access ssh, scp, and sftp (daemon is sshd):

```
# vi /etc/hosts.allow
sshd : ALL
```

Place the following entry in the /etc/hosts.deny file to deny FTP service to all clients except subnet 192.168.2.\* (this assumes the previous entry of vsftpd:192.168.2.\* exists in /etc/hosts.allow):

```
# vi /etc/hosts.deny
vsftpd : ALL
```

Use the .domain syntax to represent any hosts from a given domain. The following example allows connections to vsftpd from any host in the example.com domain (if the entry is in /etc/hosts.allow):

```
# vi /etc/hosts.allow
vsftpd : .example.com
```

If this entry appears in /etc/hosts.deny, the connection is denied.
