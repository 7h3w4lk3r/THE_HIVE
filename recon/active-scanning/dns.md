# DNS

### nslookup

run in interactive mode:

```text
>set type=a >>> set record type A (ipv4)
> set type=ns >>> set record type NS (name server)
> server [domain] >>> find the default erver for a domain
> set type=mx >>> find mail exchanger of a domain
> set type=CNAME >>> find the cannonical name of the domain
```

### dig

 mostly used to perform a zone transfer

```text
dig @[dns server] [domain]
dig -x [ip] >>> reverse lookup
dig axfr cronos.htb @[ip]
dig sec542.org -t any
```

### fierce

```text
fierce -dns [domain]

fierce -dns [domain] -wordlist [file.txt] >>> dns brute force

fierce -dns example.com -wordlist /root/tools/wordlist/SecLists/Discovery/DNS/fierce-hostlist.txt 
```

### nmap

```text
nmap -p 53 --script dns-brute zonetransfer.me
nmap -p 53 --script dns-brute zonetransfer.me --script-args=dns-brute.hostlist=[wordlist path]
```

### dnsrecon

```text
dnsrecon -d [domain] -n [name server if known] -r [range-optional] -t axfr

dnsrecon -d example.com -t axfr

dnsrecon -d example.com -D ~/list.txt -t brt >>> list contains prefixes for domain

dnsrecon -t brt -d example.com -n 127.0.0.1 -D wordlist.txt
```

### dnsmap

```text
dnsmap zonetransfer.me -w /root/tools/wordlist/SecLists/Discovery/DNS/dns-Jhaddix.txt
```

### host

```text
host -t ns zonetransfer.me
-t mx >>> mail server
-l [website] [domain name] >>> look for zone transfer
```

### dnsenum

```text
dnsenum [domain or site]
dnsenum example.com
```

### bash zone transfer

here is a simple bash script which performs a zone transfer:

```text
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













