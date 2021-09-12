# Rlogin

#### This service was mostly used in the old days for remote administration but now because of security issues this service has been replaced by the slogin and the ssh. 

{% hint style="info" %}
rlogin runs on  TCP port 513. its highly unlikely to see this service out in the open these days, but if you do, its your lucky day because if a system with an open rlogin service is exposed to the internet then there are probably more dangerous and vulnerable services running on that network. 
{% endhint %}

## Login

```text
rlogin -l root 192.168.99.131  --> no password required :|
```

## Bruteforce

```text
metasploit:
auxiliary/scanner/rservices/rlogin_login 

nmap:
nmap -sV -A -p 131 [ip]
rlogin-brute.nse
```



