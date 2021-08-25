# Active \(scanning\)

#### Scanning or active enumeration is the phase where the attacker begins to “touch” the systems and possibly leaving some traces behind. however there is no way to do this without leaving any footprints behind.

 The active recon chain is somehow pretty obvious, you find the target \( might have done this in the passive recon phase\), trace the route to target IP or network and try to map the network as best as you can, search for open ports and services, if its a web app try using it and viewing the source code or use browser extensions that can give you some info about the technologies and apps behind it, finally move on to the next phase \( threat modeling or vulnerability assessment \).

## Methodology

I split this section into two categories, network and web app, because these are usually the two most important attack surfaces of the target. for each section we will discuss methods, techniques and tools for enumerating systems and their services. the network active recon will be mostly about the ports and service scans and the web app part is about digging info about the web app structure and technologies behind it. either way the goal in recon phase is to enumerate as much as we can so there is no order for performing any of these techniques.

## Useful Resources

####  [Recon Everything](https://infosecwriteups.com/recon-everything-48aafbb8987)  [payload all the things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Network%20Discovery.md)  [Hacking Tricks](https://book.hacktricks.xyz/)  [Nmap.org](https://nmap.org/)  [Nmap Cookbook](https://b-ok.asia/book/3640353/cace51)

## Tools and Techniques

#### Here are some of the tools and techniques for active enumeration and scanning different services, some of these might include some forms of password attacks or  some other hacking techniques with less effort: 

#### [0\) Host discovery and network mapping](https://7h3w4lk3r.gitbook.io/the-hive/recon/active-scanning/host-discovery-and-mapping)

#### [1\) Web application enumeration](https://7h3w4lk3r.gitbook.io/the-hive/recon/active-scanning/web-application-enumeration)   [2\) DNS](https://7h3w4lk3r.gitbook.io/the-hive/recon/active-scanning/dns)  [ 3\) FTP](https://7h3w4lk3r.gitbook.io/the-hive/recon/active-scanning/ftp)   [4\) SNMP](https://7h3w4lk3r.gitbook.io/the-hive/recon/active-scanning/snmp)   [5\) LDAP](https://7h3w4lk3r.gitbook.io/the-hive/recon/active-scanning/ldap)   [6\) SMTP / POP3](https://7h3w4lk3r.gitbook.io/the-hive/recon/active-scanning/smtp-pop3)   [7\) SMB ](https://7h3w4lk3r.gitbook.io/the-hive/recon/active-scanning/smb)  [8\) NTP](https://7h3w4lk3r.gitbook.io/the-hive/recon/active-scanning/ntp)   [9\) RDP](https://7h3w4lk3r.gitbook.io/the-hive/recon/active-scanning/rdp)   [10\) RPC](https://7h3w4lk3r.gitbook.io/the-hive/recon/active-scanning/rpc)   11\) SSH   12\) Telnet   13\) NFS   14\) rlogin   15\) Firewall Testing   16\) OS Version and Type Fingerprinting



## Vulnerability Scanners

Although using vulnerability scanners is not usual in advanced pentesting or red team engagements, its useful to know about different vulnscanners out there:

#### [0\) Nessus \( Commertial/Free Trial \)](https://www.tenable.com/products/nessus)   [1\) Nexpose \( Commertial/Free Trial \)](https://www.rapid7.com/try/nexpose/)   [2\) OpenVAS \(Free\)](https://www.openvas.org/)   [3\) Nmap NSE scripts \(Free\)](https://nmap.org/book/man-nse.html)   [4\) Nikto \(Free\)](https://github.com/sullo/nikto)   [5\) BurpSuite Scanner \( Free and commertial \)](https://portswigger.net/burp/documentation/desktop/getting-started/proxy-troubleshooting)   [6\) OWASP Zaproxy scanner \(Free\)](https://www.zaproxy.org/)   [7\) Acunetix \( Commertial/Free Trial \)](https://www.acunetix.com/)   [8\) Netsparker \( Commertial/Free Trial \)](https://www.netsparker.com/) 













### 

