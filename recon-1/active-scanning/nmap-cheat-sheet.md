# ⭕ nmap cheat sheet

### Full commands examples <a href="#full-commands-examples" id="full-commands-examples"></a>

```bash
# Ping scan
nmap -sP 192.168.0.0/24

# Quick scan
nmap -T4 -F 192.168.1.1 -vvv

# Quick scan plus (more info but more aggressive)
nmap -sV -T4 -O -F –version-light 192.168.1.1 -vvv

# TCP Syn and UDP Scan (requires root)
nmap -sS -sU -PN -p T:80,T:445,U:161 192.168.1.1

# Soft nmap
nmap -v -Pn -n -T4 -sT -sV --version-intensity=5 --reason 192.168.1.1

# Full nmap
nmap -v -Pn -n -T4 -sT -p- --reason 192.168.1.1

# Dedicated nmap
nmap -v -Pn -n -T4 -sV --version-intensity=5 -sT -p T:ports_found --reason <IP>
```

### Extracting Live IPs from Nmap Scan

```bash
nmap 10.1.1.1 --open -oG scan-results; cat scan-results | grep "/open" | cut -d " " -f 2 > exposed-services-ips
```

### Target specification <a href="#target-specification" id="target-specification"></a>

```bash
nmap 192.168.1.1
nmap 192.168.1.1-10
nmap 192.168.1.0/24
nmap google.com
nmap 192.168.1.0/24 --exclude192.168.1.1
nmap -iL targets.txt
```

### Scan techniques <a href="#scan-techniques" id="scan-techniques"></a>

```bash
# TCP SYN port scan (default, root needed)
nmap -sS 192.168.1.1

# TCP CONNECT port scan (default without root privilege)
# Require full connection so it is slower 
nmap -sT 192.168.1.1

# UDP port scan
nmap -sU 192.168.1.1

nmap -sA 192.168.1.1
nmap -sW 192.168.1.1
nmap -sN 192.168.1.1

# Ping scan
nmap -sP 192.168.0.0/24
```

### Host discovery <a href="#host-discovery" id="host-discovery"></a>

```bash
# No scan, only list targets (get hostnames)
nmap -sL 192.168.1.1

# Disable port scanning, only host discovery
nmap -sn 192.168.1.1

# Disable host discovery, only port scanning, can be usefull if firewall deny PING
nmap -Pn 192.168.1.1

# Disable DNS resolution
nmap 192.168.1.1 -n
```

### Services, ports and OS (fingerprinting <a href="#services-ports-and-os-fingerprinting" id="services-ports-and-os-fingerprinting"></a>

```bash
nmap -p 20 192.168.1.1
nmap -p 20-100 192.168.1.1
nmap -p U:53,T:25-100 192.168.1.1
nmap -p http,https 192.168.1.1

# All ports
nmap -p- 192.168.1.1

# Fast port scan (100 more common ports)
nmap 192.168.1.1 -F

# Top X ports
nmap 192.168.1.1 --top-ports 2000

# Try to get service version
nmap 192.168.1.1 -sV

# 0-9
nmap 192.168.1.1 -sV --version-intensity 3

# Light mode but faster
nmap 192.168.1.1 -sV --version-light

# Equivalent to version-intensity 9. Harder
nmap 192.168.1.1 -sV --version-all

# Aggressive mode (OS Detection, version, script, traceroute)
nmap 192.168.1.1 -A

# OS Detection using TCP/IP
nmap 192.168.1.1 -O 

# Disable OS dection if at least one open and one closed port are not found
nmap 192.168.1.1 -O --osscan-limit

# OS Scan guess more aggressive
nmap 192.168.1.1 -O --osscan-guess

# Set the maximum number x of OS detection tries against a target 
nmap 192.168.1.1 -O --max-os-tries 2
```

### NSE Scripts <a href="#nse-scripts" id="nse-scripts"></a>

```bash
# Default script scanning, considered safe
nmap 192.168.1.1 -sC
nmap 192.168.1.1 --script default

nmap 192.168.1.1 --script=xxx
nmap 192.168.1.1 --script=xxx --script-args xx=xx

# Scan default, but remove intrusive scripts
nmap 192.168.1.1 --script “not intrusive"
```

### MISC <a href="#misc" id="misc"></a>

```bash
# Scan speed
# T0-T1 : Slow (useful for Intrusion Detection Systems evasion)
# T2-T3 : Normal
# T4-T5 : Agressive (Need a realiable and strong network)
nmap 192.168.1.1 -T0

nmap 192.168.1.1 --host-timeout 10s

# Delay between probes
nmap 192.168.1.1 --scan-delay 1s
nmap 192.168.1.1 --max-scan-delay 2s

nmap 192.168.1.1 --max-retries 3

# No faster or no slower than 100 packets/second
nmap 192.168.1.1 --min-rate 100
nmap 192.168.1.1 --max-rate 100

# If you need to scan a large network in a short period of time
# You can set up a timeout value for connection attemps
https://nmap.org/book/ncat-man-timing-options.html
nmap 192.168.0.0/16  --host-timeout <msec>
```

### Evading IDS <a href="#evading-ids" id="evading-ids"></a>

```bash
# Tiny fragmented packets
nmap 192.168.1.1 -f

# Set your own offset size
nmap 192.168.1.1 -mtu 32

# Scan from spoofed IP
nmap 192.168.1.1 -D 192.168.1.2

# Scan Facebook from Microsoft
nmap -S www.microsoft.com www.facebook.com

# Use a specific source port
nmap 192.168.1.1 -g 53

# Proxy
nmap 192.168.1.1 --proxies http://X.X.X.X:8080

# Append random data to sent packets
nmap 192.168.1.1 --data-length 200
```

### Output <a href="#output" id="output"></a>

```bash
# Save result (oN=normal oX=xml oG=grepable oA=all)
nmap 192.168.1.1 -oN scanResult.file

# Verbosity level (one v or more) and debugging level
nmap 192.168.1.1 -vvvvvv
nmap 192.168.1.1 -ddd

# Reason for the port state (equivalent to -vv)
nmap 192.168.1.1 --reason

# Show only open ports
nmap 192.168.1.1 --open

# Show all packets sent and received
nmap 192.168.1.1 --packets-trace

# Show the host interface and routes
nmap 192.168.1.1 --iflist

# Resume a scan
nmap --resume scan.file
nmap 192.168.1.1 -vvvvvv
nmap 192.168.1.1 -vvvvvv
```
