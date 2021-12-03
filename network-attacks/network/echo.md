---
description: ( TCP/UDP 7 )
---

# Echo

## :information\_source: Introduction

[Echo](https://tools.ietf.org/tools/rfcmarkup/rfcmarkup.cgi?rfc=862)

A debugging service running on UDP/TCP port 7. once a connection is established any data received is sent back. This continues until the calling user terminates the connection.

No authentication or limitation, it can be used to perform a DoS attack on target system. very old and rare to see from the internet.

```
nc -uvn <IP> 7
```
