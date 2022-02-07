# ⭕ Other Methods

## netcat

#### push files from client to listener (and vice versa):

```
listener: nc -nvlp [local port] > [out file]
clinet: nc [ip] [remote port] < [in file]
```

### netcat HTTP server

```
nc -kl 8000 --sh-exec "echo -e 'HTTP/1.1 200 OK\r\n'; date"
```

#### this will serve a single webpage.

```
while true; do nc -l -p 80 -q 1 < error.html; done
```

## Powercat File Transfers

```
nc -lnvp 443 > receiving_powercat.ps1
powercat -c 10.11.0.4 -p 443 -i C:\Users\Offsec\powercat.ps1
```

## ncat Secure Transfer

```
ncat -nvlp port --ssl > out-file
ncat -nv target-ip port --ssl < file-to-send
```

## LOLBines For Windows

#### check out these windows native tools/utilities for file transfer (download)

{% embed url="https://lolbas-project.github.io/#/download" %}
