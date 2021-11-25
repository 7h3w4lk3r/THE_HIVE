# Web Server Discovery

## Find Active Sites (Screenshots)

In a lot of scenarios network scanners like nmap discovers open web service ports like 80 or 443. in a large scale assessment we are not able to manually check all hosts to see if an active web site is hosted or not. the best way is to use automated tools to take screenshots from these ports:

### httpscreenshot

{% embed url="https://github.com/breenmachine/httpscreenshot" %}

```
httpscreenshot -l domains-https.txt -tG -sF -vH 
```

### eyewitness

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

## Web Server Info

```
whatweb example.com -v
```

## Test HTTP Strict Transport Security (HSTS)



```
curl -s -D- https://domain.com/ | grep Strict

Result expected:
Strict-Transport-Security: max-age=...
```





