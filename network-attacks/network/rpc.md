# MSRPC / RPCbind

## :information\_source: Introduction

#### [Remote Procedure Call](https://datatracker.ietf.org/doc/html/rfc5531)

**A**n interprocess communication technique. The Full form of RPC is Remote Procedure Call. It is used for client-server applications. RPC mechanisms are used when a computer program causes a procedure or subroutine to execute in a different address space, which is coded as a normal procedure call without the programmer specifically coding the details for the remote interaction.

This procedure call also manages low-level transport protocol, such as User Datagram Protocol, Transmission Control Protocol/Internet Protocol etc. It is used for carrying the message data between programs.

### Types of RPC

Three types of RPC are:

* Callback RPC
* Broadcast RPC
* Batch-mode RPC

### RPC Architecture

RPC architecture has mainly five components of the program:

1. **Client**
2. **Client Stub**
3. **RPC Runtime**
4. **Server Stub**
5. **Server**

![](<../../.gitbook/assets/image (282) (1) (1).png>)

## :ballot\_box\_with\_check: Checklist

* [ ] Check for Null sessions
* [ ] Quert RPC locator
* [ ] Test [NFS service ](nfs.md)if you found any
* [ ] Try exploiting NIS if you found any
* [ ] Try bypassing port mapper filter

## Enumeration

```
nmap -sSUC -p111 [ip]
rpcinfo domain.local
nmap --script bitcoinrpc-info, srpc-enum,rpcap-info,rpc-grind,rpcinfo [ip]
auxiliary/scanner/dcerpc/tcp_dcerpc_auditor
auxiliary/scanner/dcerpc/endpoint_mapper
auxiliary/scanner/dcerpc/hidden 
auxiliary/gather/windows_deployment_services_shares
auxiliary/scanner/dcerpc/windows_deployment_services 
auxiliary/scanner/nessus/nessus_xmlrpc_ping
auxiliary/scanner/dcerpc/management
```

## Check for Null user sessions

```
rpcclient -N -U "" [IP address]
rpcclient $> lookupnames [user] → check username
rpcinfo -p [ip]
```

## automate user enumeration with a list of usernames

a simple loop to check RPC users in bash

```
for u in $(cat users.txt); do rpcclient -U "" 192.168.13.26 -N --command="lookupnames $u"; done |grep "User: 1"
```

### metasploit

```
use auxiliary/scanner/dcerpc/endpoint_mapper use auxiliary/scanner/dcerpc/hidden use auxiliary/scanner/dcerpc/management use auxiliary/scanner/dcerpc/tcp_dcerpc_auditor
```

## rpcdump

rpcdump and ifids Windows utilities query both the RPC locator and specific RPC endpoints to list IFID values.

```
D:\rpctools> rpcdump [-p port] 192.168.189.1
IfId: 5a7b91f8-ff00-11d0-a9b2-00c04fb6e6fc version 1.0
Annotation: Messenger Service
UUID: 00000000-0000-0000-0000-000000000000
Binding: ncadg_ip_udp:192.168.189.1[1028]
```

## NIS

If you find the service `ypbind`running:

![](https://github.com/carlospolop/hacktricks/raw/master/.gitbook/assets/image%20\(233\).png)

You can try to exploit it. Anyway, first of all you will **need to guess the NIS "domain name"** of the machine (when NIS is installed it's configured a "domain name") and **without knowing this domain name you cannot do anything**.

Upon obtaining the NIS domain name for the environment (example.org in this case), use the ypwhich command to ping the NIS server and ypcat to obtain sensitive material. You should feed encrypted password hashes into John the Ripper, and once cracked, you can use it to evaluate system access and privileges.

```
root@kali:~# apt-get install nis
root@kali:~# ypwhich -d example.org 192.168.10.1
potatohead.example.org
root@kali:~# ypcat –d example.org –h 192.168.10.1 passwd.byname
tiff:noR7Bk6FdgcZg:218:101::/export/home/tiff:/bin/bash 
katykat:d.K5tGUWCJfQM:2099:102::/export/home/katykat:/bin/bash 
james:i0na7pfgtxi42:332:100::/export/home/james:/bin/tcsh 
florent:nUNzkxYF0Hbmk:199:100::/export/home/florent:/bin/csh 
dave:pzg1026SzQlwc:182:100::/export/home/dave:/bin/bash 
yumi:ZEadZ3ZaW4v9.:1377:160::/export/home/yumi:/bin/bash
```

| **Master file**  | **Map(s)**                  | **Notes**                         |
| ---------------- | --------------------------- | --------------------------------- |
| /etc/hosts       | hosts.byname, hosts.byaddr  | Contains hostnames and IP details |
| /etc/passwd      | passwd.byname, passwd.byuid | NIS user password file            |
| /etc/group       | group.byname, group.bygid   | NIS group file                    |
| /usr/lib/aliases | mail.aliases                | Details mail aliases              |

## Bypass Filtered Portmapper port

{% embed url="https://medium.com/@sebnemK/how-to-bypass-filtered-portmapper-port-111-27cee52416bc" %}

## Login Brute Force

```
auxiliary/scanner/msf/msf_rpc_login
auxiliary/scanner/nessus/nessus_rest_login 
auxiliary/scanner/nessus/nessus_xmlrpc_login
rpcap-brute.nse
deluge-rpc-brute.nse
rpcap-brute.nse
nessus-xmlrpc-brute.nse
```

