---
description: ( TCP 23 )
---

# ⭕ Telnet

## <mark style="color:red;">connect and test</mark>

```
telnet [ip] 23
```

## <mark style="color:red;">metasploit</mark>

```
search auxiliary/scanner/telnet
auxiliary/scanner/telnet/lantronix_telnet_version
auxiliary/scanner/telnet/telnet_login
```

## <mark style="color:red;">nmap</mark>

```
nmap -p 23 --script telnet-brute --script-args userdb=[userlist],passdb=[wordlist],telnet-brute.timeout=8s [ip]

nmap -n -sV -Pn --script "*telnet* and safe" -p 23 <IP>

scripts:
telnet-brute.nse
telnet-encryption.nse
telnet-ntlm-info.nse
```

## <mark style="color:red;">Login Brute Force</mark>

```
hydra -l root -P passwords.txt [-t 32] <IP> telnet
ncrack -p 23 --user root -P passwords.txt <IP> [-T 5]
medusa -u root -P 500-worst-passwords.txt -h <IP> -M telnet
```
