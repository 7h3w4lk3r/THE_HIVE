---
description: (TCP/UDP 53)
---

# ⭕ DNS

## :information\_source: <mark style="color:blue;">Introduction</mark>

#### [Domain Name Service](https://tools.ietf.org/html/rfc1035)

TCP port 53 by default, fall back to UDP port 53 if not possible.

![](<../../.gitbook/assets/image (277) (1) (1) (1) (1) (1).png>)

## :ballot\_box\_with\_check: <mark style="color:blue;">Checklist</mark>

* [ ] Use OSINT for DNS records
* [ ] [Check forward and reverse lookups](dns.md#dns-enumeration)
* [ ] [Test zone transfer](dns.md#bash-zone-transfer)
* [ ] [Test NSEC / NSEC3](dns.md#nsec-nsec3)
* [ ] [Look for CVEs](dns.md#known-vulnerabilities)

## <mark style="color:red;">Quick Check</mark>

```
whois domain.com
dig {a|txt|ns|mx} domain.com
dig {a|txt|ns|mx} domain.com @ns1.domain.com
host -t {a|txt|ns|mx} megacorpone.com
host -a megacorpone.com
host -l megacorpone.com ns1.megacorpone.com
dnsrecon -d megacorpone.com -t axfr @ns2.megacorpone.com
dnsenum domain.com
nslookup -> set type=any -> ls -d domain.com
for sub in $(cat subdomains.txt);do host $sub.domain.com|grep "has.address";done
```

## <mark style="color:red;">DNS Enumeration</mark>

### <mark style="color:orange;">nslookup</mark>

run in interactive mode:

```
>set type=a >>> set record type A (ipv4)
> set type=ns >>> set record type NS (name server)
> server [domain] >>> find the default erver for a domain
> set type=mx >>> find mail exchanger of a domain
> set type=CNAME >>> find the cannonical name of the domain
```

### <mark style="color:orange;">dig</mark>

mostly used to perform a zone transfer

```
dig @[dns server] [domain]
dig -x [ip] >>> reverse lookup
dig axfr cronos.htb @[ip]
dig sec542.org -t any
```

### <mark style="color:orange;">fierce</mark>

```
fierce -dns [domain]

fierce -dns [domain] -wordlist [file.txt] >>> dns brute force

fierce -dns example.com -wordlist /root/tools/wordlist/SecLists/Discovery/DNS/fierce-hostlist.txt 
```

### <mark style="color:orange;">nmap</mark>

```
nmap -p 53 --script dns-brute zonetransfer.me
nmap -p 53 --script dns-brute zonetransfer.me --script-args=dns-brute.hostlist=[wordlist path]
```

### <mark style="color:orange;">dnsrecon</mark>

* `dnsrecon -d [domain]` - Displays S0A, NS, A , AAAA , MX, and SRV of the target domain
* `dnsrecon -d [domain] -t rvl` - Performs reverse DNS lookup for IP address or CIDR range
* `dnsrecon -d [domain] -t axfr` - Attempts a zone transfer of all NS record nameservers
* dn`srecon -d [domain] -t zonewalk` - Performs a DNSSEC zone walk by querying for NSEC records
* `dnsrecon -d [domain] -t snoop` - D \[dictionary file] - Scans for DNS cache snooping using a supplied dictionary file

#### <mark style="color:green;">DNSRecon can also perform subdomain brute forcing with a dictionary using the following command:</mark>

• dnsrecon -d \[domain ] -t brt - D \[dictionary file]

#### <mark style="color:green;">Finally DNSRecon can output the returned data to an XML file using the — xml \[output file] flag or to an SQLite database using the db \[output file] flag</mark>

```
# examples:

dnsrecon -d [domain] -n [name server if known] -r [range-optional] -t axfr
dnsrecon -d example.com -t axfr
dnsrecon -d example.com -D ~/list.txt -t brt >>> list contains prefixes for domain
dnsrecon -t brt -d example.com -n 127.0.0.1 -D wordlist.txt
```

### <mark style="color:orange;">dnsmap</mark>

```
dnsmap zonetransfer.me -w /root/tools/wordlist/SecLists/Discovery/DNS/dns-Jhaddix.txt
```

### <mark style="color:orange;">host</mark>

```
host -t ns zonetransfer.me
-t mx >>> mail server
-l [website] [domain name] >>> look for zone transfer
```

### <mark style="color:orange;">dnsenum</mark>

```
dnsenum [domain or site]
dnsenum example.com
```

### <mark style="color:orange;">Identifying private addresses by using dig</mark>

```
dig @ns3.isc-sns.info -f /tmp/paypal.txt +noall +answer | awk '{printf("%s %s\n",$5,$1);}' | grep -E '^(10\.)'
```

### <mark style="color:orange;">bash zone transfer</mark>

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

### <mark style="color:orange;">IPv6</mark>

Brute force using "AAAA" requests to gather IPv6 of the subdomains.

```
dnsdict6 -s -t <domain>
```

Bruteforce reverse DNS in usi ng IPv6 addresses

```
dnsrevenum6 pri.authdns.ripe.net 2001:67c:2e8::/48 #Will use the dns pri.authdns.ripe.net
```

## <mark style="color:red;">NSEC / NSEC3</mark>

You can quiz name servers supporting DNSSEC to reveal valid hostnames. Scripts that automate this are dns-nsec-enum and dns-nsec3-enum

```
nmap -sSU -p53 --script dns-nsec-enum --script-args dns-nsec-enum.domains=paypal.com ns3.isc-sns.info

```

## <mark style="color:red;">DNS Recursion DDoS</mark>

If **DNS recursion is enabled**, an attacker could **spoof** the **origin** on the UDP packet in order to make the **DNS send the response to the victim server**. An attacker could abuse **ANY** or **DNSSEC** record types as they use to have the bigger responses.\
The way to **check** if a DNS supports **recursion** is to query a domain name and **check** if the **flag "ra"** (_recursion available_) is in the response

```
dig google.com A @<IP>
```

## <mark style="color:red;">Known Vulnerabilities</mark>

### <mark style="color:orange;">SIGRed</mark>

{% embed url="https://research.checkpoint.com/2020/resolving-your-way-into-domain-admin-exploiting-a-17-year-old-bug-in-windows-dns-servers" %}

### <mark style="color:orange;">Simple DNS Plus Remote DoS</mark>

{% embed url="https://www.cvedetails.com/vulnerability-list/vendor_id-8348/product_id-14553/Simpledns-Simple-Dns-Plus.html" %}

{% embed url="https://www.exploit-db.com/exploits/6059" %}
