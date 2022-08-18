# ⭕ Cobalt Strike Pivoteing

This technique is the same a metasploit reverse proxy method but here we use a CS beacon instead of meterpreter.

create a socks proxy with the beacon:

```
socks 8090
```

add the entry to `proxychains.conf`

```
socks4 127.0.0.1 8090
```

use the tunnel:

```
proxychains xfreerdp /v:127.0.0.1:3389 /u:password
```
