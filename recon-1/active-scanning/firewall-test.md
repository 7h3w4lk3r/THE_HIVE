# Firewall Test

## Port Knocking

Port-knocking the a obfuscation-as-security technique. I basically means that after knocking on ports in a specific sequence a certain port will open automatically. It seems to be more popular in Capture-the-flag contests than real life networks. But I have included it anyways, since CTF:s are great.

This is a way to hide certain ports, so you don't get unwanted intrusion-intents.

When you "knock" on a port you are really just sending TCP-packets with `SYN`-flag to that port. The closed port will then respond with a `ACK/RST`. Which basically means that the host has received the `TCP`-packet, and it ACKnolwdge it, but responds with a Reset (`RST`) flag. `RST` just means that the port is closed.

## Methods

### Knock

```
apt install knockd
knock [targert ip] [port]
knock 192.168.56.101 4000 5000 6000
```

rescan the system to see if any ports are now open, try to connect to new ports:

```
nc 192.168.1.102 8888
```

### nmap

```
for x in 4000 5000 6000; do nmap -Pn --host_timeout 201 --max-retries 0 -p $x server_ip_address; done
```

### Netcat

```
nc 192.168.1.102 4000
nc 192.168.1.102 5000
nc 192.168.1.102 6000
nc 192.168.1.102 8888
```
