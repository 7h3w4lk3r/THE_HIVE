# 🔧 Other Tools

## plink (windows)

```
plink.exe -l root -R 445:127.0.0.1:445 YOURIPADDRESS
```

## Ncat

```
# Ncat =/= netcat
# Enhanced version, developped by nmap dev and often packaged with
# Ciphered connexion, IP restriction
# To pivot, needed on both attacker and pivot machine

# Attacker (broker == accept multiple connexions)
$ ncat -lv --broker --max-conns 2

# Pivot
# We connect to attacker and target
$ ncat -v 192.168.2.149 31337 -c 'ncat -v 10.42.42.2 80'
```

## PivoteSuite

```
# Similar features to other tools but support multilevel pivot
# Like Metasploit

# "Remote" local port forwarding
# On the pivot
$ pivotsuite -S -F --server-option=PF --forward-ip=10.42.42.2 --forward-port=80 --server-ip=192.168.2.105 --server-port=8080

# "Remote" dynamic port forwarding
$ pivotsuite -S -F --server-option=SP --server-ip=192.168.2.105 --server-port=8080
```

## RINETD

```
 sudo apt install rinetd
```

edit the config file:

```
nano /etc/rinetd.conf
```

![](<../../.gitbook/assets/image (268).png>)

```
service rinetd restart
```

bind address is attacker machine bind port is the listening incoming port connectaddress is the address to forward the traffic to and connectport is the target port.

## Other Resources

* [NetworkPivotingTechniques](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Network%20Pivoting%20Techniques.md)
* [Abachy's Port Forwarding Guide](https://www.abatchy.com/2017/01/port-forwarding-practical-hands-on-guide)&#x20;
* [http://woshub.com/port-forwarding-in-windows/](http://woshub.com/port-forwarding-in-windows/)
* [https://www.offensive-security.com/metasploit-unleashed/portfwd/](https://www.offensive-security.com/metasploit-unleashed/portfwd/)
* [https://pentest.blog/explore-hidden-networks-with-double-pivoting/](https://pentest.blog/explore-hidden-networks-with-double-pivoting/)
* [https://fumenoid.github.io/posts/port-forwarding](https://fumenoid.github.io/posts/port-forwarding)
* [https://chamibuddhika.wordpress.com/2012/03/21/ssh-tunnelling-explained/](https://chamibuddhika.wordpress.com/2012/03/21/ssh-tunnelling-explained/)
* [https://www.offensive-security.com/metasploit-unleashed/proxytunnels/](https://www.offensive-security.com/metasploit-unleashed/proxytunnels/)
* [https://0xdf.gitlab.io/2019/01/28/pwk-notes-tunneling-update1.html](https://0xdf.gitlab.io/2019/01/28/pwk-notes-tunneling-update1.html)
* [https://www.cynet.com/attack-techniques-hands-on/how-hackers-use-icmp-tunneling-to-own-your-network/](https://www.cynet.com/attack-techniques-hands-on/how-hackers-use-icmp-tunneling-to-own-your-network/)
* [https://xapax.github.io/security/#random\_tips\_and\_tricks/port\_forwarding\_and\_tunneling/](https://xapax.github.io/security/#random\_tips\_and\_tricks/port\_forwarding\_and\_tunneling/)
* [https://xapax.github.io/security/#random\_tips\_and\_tricks/pivoting/](https://xapax.github.io/security/#random\_tips\_and\_tricks/pivoting/)

## Other Tools

* [PivotSuite](https://github.com/RedTeamOperations/PivotSuite) - PivotSuite is a portable, platform independent and powerful network pivoting toolkit, Which helps Red Teamers / Penetration Testers to use a compromised system to move around inside a network.
* [Modaliska](https://github.com/drk1wi/Modlishka)  - Modlishka is a powerful and flexible HTTP reverse proxy. It implements an entirely new and interesting approach of handling browser-based HTTP traffic flow, which allows to transparently proxy multi-domain destination traffic, both TLS and non-TLS, over a single domain, without a requirement of installing any additional certificate on the client.
* [Iodine](https://github.com/yarrick/iodine) - This is a piece of software that lets you tunnel IPv4 data through a DNS server. This can be usable in different situations where internet access is firewalled, but DNS queries are allowed.
* [Mallory](https://github.com/justmao945/mallory) - HTTP/HTTPS proxy over SSH.
* [Pivotnacci](https://github.com/blackarrowsec/pivotnacci) - Pivot into the internal network by deploying HTTP agents.
* [PacketWhisper](https://github.com/TryCatchHCF/PacketWhisper) - PacketWhisper: Stealthily exfiltrate data and defeat attribution using DNS queries and text-based steganography. Avoid the problems associated with typical DNS exfiltration methods. Transfer data between systems without the communicating devices directly connecting to each other or to a common endpoint. No need to control a DNS Name Server.
* [chisel](https://www.kali.org/tools/chisel/) - This package contains a fast TCP/UDP tunnel, transported over HTTP, secured via SSH. Single executable including both client and server. Chisel is mainly useful for passing through firewalls, though it can also be used to provide a secure endpoint into your network.
* [cryptcat](https://www.kali.org/tools/cryptcat/) - Cryptcat is a simple Unix utility which reads and writes data across network connections, using TCP or UDP protocol while encrypting the data being transmitted.
* [dns2tcp](https://www.kali.org/tools/dns2tcp/) - dns2tcp is a set of tools to encapsulate a TCP session in DNS packets. This type of encapsulation generates smaller packets than IP-over-DNS, improving throughput.
* [dnschef](https://www.kali.org/tools/dnschef/) - DNSChef is a highly configurable DNS proxy for Penetration Testers and Malware Analysts.
* [iodine](https://www.kali.org/tools/iodine/) - This is a piece of software that lets you tunnel IPv4 data through a DNS server. This can be usable in different situations where internet access is firewalled, but DNS queries are allowed.
* [miredo](https://www.kali.org/tools/miredo/) - A client for the Teredo IPV6 tunneling protocol.
* [nextnet](https://www.kali.org/tools/nextnet/) - This package contains a pivot point discovery tool written in Go.
* [redsocks](https://www.kali.org/tools/redsocks/) - Redsocks is a daemon running on the local system, that will transparently tunnel any TCP connection via a remote SOCKS4, SOCKS5 or HTTP proxy server.
* [sslh](https://www.kali.org/tools/sslh/) - sslh lets one accept HTTPS, SSH, OpenVPN, tinc and XMPP connections on the same port. This makes it possible to connect to any of these servers on port 443 (e.g. from inside a corporate firewall, which almost never block port 443) while still serving HTTPS on that port
