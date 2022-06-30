# ⭕ SSL Certs

## <mark style="color:red;">Test SSL certs</mark>

Not really important as an attack vector but its something you should definitely include in your report. since the SSL rating of the target organization website is valuable to the customers/partners and can be a problem from business aspects.

{% embed url="http://ssllab.com/" %}

## <mark style="color:red;">nmap</mark>

```
nmap -p 443 --script=ssl-enum-ciphers target.com
```
