# unshadow

```text
unshadow /etc/passwd /etc/shadow > hashes.txt

john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt

hashcat -m 1800 -a 0 -o cracked.txt hashes.txt /usr/share/wordlists/rockyou.txt

john --format=sha512crypt --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

john --format=sha512crypt hash.txt --show
```

