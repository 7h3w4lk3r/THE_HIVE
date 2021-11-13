---
description: (TCP/UDP 53)
---

# DNS

## :information\_source: Introduction

#### [Domain Name Service](https://www.cloudflare.com/learning/dns/what-is-dns/)

TCP port 53 by default, fall back to UDP port 53 if not possible.

## :ballot\_box\_with\_check: Checklist

* [ ] Use OSINT for DNS records
* [ ] Check forward and reverse lookups
* [ ] Test zone transfer
* [ ] Look for CVEs&#x20;

## DNS Enumeration

### nslookup

run in interactive mode:

```
>set type=a >>> set record type A (ipv4)
> set type=ns >>> set record type NS (name server)
> server [domain] >>> find the default erver for a domain
> set type=mx >>> find mail exchanger of a domain
> set type=CNAME >>> find the cannonical name of the domain
```

### dig

&#x20;mostly used to perform a zone transfer

```
dig @[dns server] [domain]
dig -x [ip] >>> reverse lookup
dig axfr cronos.htb @[ip]
dig sec542.org -t any
```

### fierce

```
fierce -dns [domain]

fierce -dns [domain] -wordlist [file.txt] >>> dns brute force

fierce -dns example.com -wordlist /root/tools/wordlist/SecLists/Discovery/DNS/fierce-hostlist.txt 
```

### nmap

```
nmap -p 53 --script dns-brute zonetransfer.me
nmap -p 53 --script dns-brute zonetransfer.me --script-args=dns-brute.hostlist=[wordlist path]
```

### dnsrecon

```
dnsrecon -d [domain] -n [name server if known] -r [range-optional] -t axfr

dnsrecon -d example.com -t axfr

dnsrecon -d example.com -D ~/list.txt -t brt >>> list contains prefixes for domain

dnsrecon -t brt -d example.com -n 127.0.0.1 -D wordlist.txt
```

### dnsmap

```
dnsmap zonetransfer.me -w /root/tools/wordlist/SecLists/Discovery/DNS/dns-Jhaddix.txt
```

### host

```
host -t ns zonetransfer.me
-t mx >>> mail server
-l [website] [domain name] >>> look for zone transfer
```

### dnsenum

```
dnsenum [domain or site]
dnsenum example.com
```

### bash zone transfer

here is a simple bash script which performs a zone transfer:

```
#!/bin/bash
if [ -z "$1" ]; then
echo "[*] Simple Zone transfer script [*]"
echo "Usage : $0 [domain name] "
exit 0
fi
for server in $(host -t ns $1 | cut -d " " -f4); do
host -l $1 $server |grep "has address"
done
```

### IPv6

Brute force using "AAAA" requests to gather IPv6 of the subdomains.

```
dnsdict6 -s -t <domain>
```

Bruteforce reverse DNS in using IPv6 addresses

```
dnsrevenum6 pri.authdns.ripe.net 2001:67c:2e8::/48 #Will use the dns pri.authdns.ripe.net
```

## DNSSec

```
nmap -sSU -p53 --script dns-nsec-enum --script-args dns-nsec-enum.domains=paypal.com ns3.isc-sns.info
```

## DNS Recursion DDoS

If **DNS recursion is enabled**, an attacker could **spoof** the **origin** on the UDP packet in order to make the **DNS send the response to the victim server**. An attacker could abuse **ANY** or **DNSSEC** record types as they use to have the bigger responses.\
The way to **check** if a DNS supports **recursion** is to query a domain name and **check** if the **flag "ra"** (_recursion available_) is in the response

```
dig google.com A @<IP>
```

## Known Vulnerabilities

### SIGRed

{% embed url="https://research.checkpoint.com/2020/resolving-your-way-into-domain-admin-exploiting-a-17-year-old-bug-in-windows-dns-servers" %}

### Simple DNS Plus Remote DoS

{% embed url="https://www.cvedetails.com/vulnerability-list/vendor_id-8348/product_id-14553/Simpledns-Simple-Dns-Plus.html" %}

{% embed url="https://www.exploit-db.com/exploits/6059" %}
