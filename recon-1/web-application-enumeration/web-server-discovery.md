# ⏩ Web Server Discovery

## <mark style="color:red;">Find Active Sites (Screenshots)</mark>

In a lot of scenarios network scanners like nmap discovers open web service ports like 80 or 443. in a large scale assessment we are not able to manually check all hosts to see if an active web site is hosted or not. the best way is to use automated tools to take screenshots of discovered web servers.

### <mark style="color:orange;">httpscreenshot</mark>

{% embed url="https://github.com/breenmachine/httpscreenshot" %}

```
httpscreenshot -l domains-https.txt -tG -sF -vH 
```

### <mark style="color:orange;">eyewitness</mark>

{% embed url="https://github.com/FortyNorthSecurity/EyeWitness" %}

```
apt install eyewitness

# examples:
EyeWitness.exe --help
EyeWitness.exe --bookmarks
EyeWitness.exe -f C:\Path\to\urls.txt
EyeWitness.exe --file C:\Path\to\urls.txt --delay [timeout in seconds] --compress

./EyeWitness -f urls.txt --web
./EyeWitness -x urls.xml --timeout 8 
./EyeWitness.py -f urls.txt --web --proxy-ip 127.0.0.1 --proxy-port 8080 --proxy-type socks5 --timeout 120
```

## <mark style="color:red;">Web Server Info</mark>

```
whatweb example.com -v
```

## <mark style="color:red;">Test HTTP Strict Transport Security (HSTS)</mark>

```
curl -s -D- https://domain.com/ | grep Strict

Result expected:
Strict-Transport-Security: max-age=...
```
