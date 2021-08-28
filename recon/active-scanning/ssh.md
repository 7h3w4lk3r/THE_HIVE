# SSH

## Testing Connection and Version

#### attempt to connect to see if there is a banner when connecting

```text
auxiliary/scanner/ssh/ssh_version
telnet [ip] [port]
nc -nv [ip] 22
```

also ssh login banners might be helpful some times.

## nmap Enumeration Scripts

```text
nmap -Pn -n 127.0.0.1 --script ssh-hostkey
nmap -Pn -n 127.0.0.1 --script ssh-publickey-acceptance
nmap -Pn -n 127.0.0.1 --script ssh-run
nmap -Pn -n --script ssh2-enum-algos 127.0.0.1
nmap -Pn -n --script ssh-auth-methods 127.0.0.1
nmap -Pn -n --script ssh-brute 127.0.0.1
```

## brute force

```text
hydra -l [user] -P [wordlist] [ip] ssh
ncrack -v -T 5 --user [user] -P [wordlist] [ip]:22
medusa -h [ip] [ip] -u [user] -P [wordlist] -M ssh
patator ssh_login host=192.168.56.103 user=FILE0 0=user password=/usr/share/wordlists/rockyou.txt
```

