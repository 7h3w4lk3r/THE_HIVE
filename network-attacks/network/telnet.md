# Telnet

## connect and test

```text
telnet [ip] 23
```

## metasploit

```text
search auxiliary/scanner/telnet
auxiliary/scanner/telnet/lantronix_telnet_version
auxiliary/scanner/telnet/telnet_login
```

## nmap

```text
nmap -p 23 --script telnet-brute --script-args userdb=[userlist],passdb=[wordlist],telnet-brute.timeout=8s [ip]

scripts:
telnet-brute.nse
telnet-encryption.nse
telnet-ntlm-info.nse
```



