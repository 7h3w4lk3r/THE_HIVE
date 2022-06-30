---
description: ( TCP 23 )
---

# ⭕ Telnet

## connect and test

```
telnet [ip] 23
```

## metasploit

```
search auxiliary/scanner/telnet
auxiliary/scanner/telnet/lantronix_telnet_version
auxiliary/scanner/telnet/telnet_login
```

## nmap

```
nmap -p 23 --script telnet-brute --script-args userdb=[userlist],passdb=[wordlist],telnet-brute.timeout=8s [ip]

nmap -n -sV -Pn --script "*telnet* and safe" -p 23 <IP>

scripts:
telnet-brute.nse
telnet-encryption.nse
telnet-ntlm-info.nse
```

## Login Brute Force

```
hydra -l root -P passwords.txt [-t 32] <IP> telnet
ncrack -p 23 --user root -P passwords.txt <IP> [-T 5]
medusa -u root -P 500-worst-passwords.txt -h <IP> -M telnet
```
