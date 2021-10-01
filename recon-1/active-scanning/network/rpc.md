# RPC

### Enumerate null user sessions

```text
rpcclient -N -U "" [IP address]
rpcclient $> lookupnames [user] → check username
rpcinfo -p [ip]
```

### automate user enumeration with a list of usernames

a simple loop to check RPC users in bash

```text
for u in $(cat users.txt); do rpcclient -U "" 192.168.13.26 -N --command="lookupnames $u"; done |grep "User: 1"
```

### metasploit

```text
use auxiliary/scanner/dcerpc/endpoint_mapper use auxiliary/scanner/dcerpc/hidden use auxiliary/scanner/dcerpc/management use auxiliary/scanner/dcerpc/tcp_dcerpc_auditor
```

### rpcdump

rpcdump and ifids Windows utilities query both the RPC locator and specific RPC endpoints to list IFID values.

```text
D:\rpctools> rpcdump [-p port] 192.168.189.1
IfId: 5a7b91f8-ff00-11d0-a9b2-00c04fb6e6fc version 1.0
Annotation: Messenger Service
UUID: 00000000-0000-0000-0000-000000000000
Binding: ncadg_ip_udp:192.168.189.1[1028]
```

### nmap scripts

```text
bitcoinrpc-info.nse
deluge-rpc-brute.nse
metasploit-msgrpc-brute.nse
metasploit-xmlrpc-brute.nse
msrpc-enum.nse
nessus-xmlrpc-brute.nse
rpcap-brute.nse
rpcap-info.nse
rpc-grind.nse
rpcinfo.nse
xmlrpc-methods.nse
```



