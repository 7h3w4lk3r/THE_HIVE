# WAF Detection

WAF stands for Web Application Firewall. Its goal is to protect the website behind it by filtering/monitoring the traffic. Fingerprinting is a method used to gather information (about any WAF in this context).

### Tools <a href="tools" id="tools"></a>

#### Detecting WAFs with [WAFW00F](https://github.com/EnableSecurity/wafw00f).

```
wafw00f $URL
```

#### Detecting WAFs with [WhatWaf](https://github.com/Ekultek/WhatWaf).

```
whatwaf -u $URL
```

#### Detecting WAFs with [nmap](https://nmap.org).

```
nmap -p 80,443 --script=http-waf-fingerprint $URL
```

{% hint style="info" %}
Another script called `http-waf-detect`can be used. It detects IDS/IPS/WAF but doesn't give information about the vendor, or version...
{% endhint %}

### Other examples <a href="other-examples" id="other-examples"></a>

A manual testing workflow could be to check the cookies and response headers.

**Cookies**: some WAF can be identified by the cookie's name. **Response headers**: sometimes they are changed to apparently "confuse the attacker".

### Resources <a href="resources" id="resources"></a>

{% embed url="https://nmap.org/nsedoc/scripts/http-waf-fingerprint.html" %}
