# HTTPS Downgrade / HSTS Bypass

{% embed url="https://itigic.com/how-to-decrypt-https-traffic-with-bettercap-on-linux" %}

## HTTPS

HTTPS or HTTP over SSL is relied upon as a critical network technology for many organizations, used by web browsers and many other applications to protect the confidentiality, integrity, and authenticity of data. Certificate validation for HTTPS has significantly improved in web browsers in the past few years, where earlier browsers would make it too simple for an uninformed user to accept a certificate warning without understanding the impact of doing so.

{% hint style="info" %}
#### Modern web browsers such as Firefox demonstrate a significant warning when a certificate is invalid for any reason, as shown on this slide. To bypass this warning and continue with the invalid certificate, the user must click through five options before continuing.
{% endhint %}

## HTTPS Downgrade Attack

### SSLStrip

Instead of using an invalid certificate and tricking the user into terminating the SSL session with the attacker, Sslstrip avoids any certificate warnings by rewriting all HTTP traffic to remove references to HTTPS. Sslstrip proxies all traffic intended to use SSL to the legitimate SSL server, but only responds with HTTP traffic to the victim, allowing the attacker to access all content while logging activity such as POST statements. Sslstrip also uses some clever tricks to assuage user concerns about seeing their traffic sent over HTTP instead of HTTPS.

![](<../../.gitbook/assets/image (301) (1).png>)

Sslstrip takes advantage of a common flaw in HTTPS websites: Many users start with a plain HTTP request, which is later redirected to HTTPS by the server. Consider, for example, logging in to a Gmail account. Very few users will enter "https://www.gmail.com" in the web browser, instead relying on Gmail to redirect HTTP traffic to HTTPS. However, since the user is relying on a weak protocol (HTTP) to redirect them, the security of a stronger protocol (HTTPS) is threatened.

#### Sslstrip does not allow an attacker to eavesdrop on activity sent exclusively via an HTTPS server without being redirected from an HTTP site.

#### turn on IP forwarding

```
echo 1 >/proc/sys/net/ipv4/ip_forward
```

#### redirect all HTTP (tcp 80) traffic to the sslstrip process listening on port 8080.

```
iptables -t nat -A PREROUTING -p tcp --destination-port 80 -j REDIRECT --to-port 8080
```

#### start sslstrip listening on port 8080

```
sslstrip -l 8080
```

#### become MitM through ARP spoofing with ettercap

```
ettercap -TqM arp:remote /10.10.10.1254// /10.10.10.1-254//
```

#### monitor `sslstrip.log` or watch ettercap for passwords

```
tail -f sslstrip.log
```

### Ettercap SSLStrip Plugin

```
ettercap -TqM arp:remote -P sslstrip /172.16.0.1// /172.16.0.11//
```

### Bettercap SSLStrip&#x20;

```
http.proxy on/off
# enable SSL strip attack
set http.proxy.sslstrip true/false

https.proxy on/off
# enable SSL strip attack
set https.proxy.sslstrip true/false
```

## HSTS

To address the prevalent exposure to Sslstrip attacks, the IETF introduced RFC 6797 (https://tools.ietf.org/html/rfc6797), which introduced the HTTP Strict-Transport-Security (HSTS) header option for websites. When the Strict-Transport-Security header is set on any response from a website, the browser records that the server expects all activity with the server to be transmitted over HTTPS.

The HSTS option partially mitigates Sslstrip attacks. If the victim previously visited a website that set the HSTS header and an attacker attempts to rewrite links and content to remove the https:// prefix, the browser refuses to visit the site over HTTP. In addition, sites marked as using HSTS do not show users certificate warnings with an option to continue the transaction; instead, the browser indicates an error and refuses to communicate with the HTTPS site until the certificate issue has been resolved (also eliminating the attacker's option to mount SSL MitM attacks against an HSTS site).

## HSTS Bypass

browsers match the hostname of the server requested to the list of sites that use HSTS. If the hostname of the requested URL matches a hostname known to use HSTS, the browser refuses to load the content if the URL is rewritten by Sslstrip as an HTTP link (or display a certificate error with a continue option if there is a certificate error).

![](<../../.gitbook/assets/image (302).png>)

when an initial page is loaded over HTTP, an attacker can rewrite links to strip the HTTPS links, and it can change the requested hostnames as well. In the example shown on this page, the victim starts the browser by visiting http://www.kickstarter.com. The Kickstarter website returns content and links over HTTP, which the attacker modifies in two ways: First, HTTPS links are rewritten as HTTP links, and, second, the hostname in HTTPS links is changed slightly, such as adding another "w" in "www" ("www" to "wwww") or any other modification that changes the hostname.

If the victim clicks a modified link (https://www.facebook.com from Kickstarter is modified by Sslstrip2 to be http://wwww.facebook.com), the request is automatically forwarded to the upstream site as https://www.facebook.com, but the content is delivered to the victim downstream over HTTP (again, rewriting links and hostnames to avoid HTTPS and HSTS policies). The victim's browser never raises an error about an insecure site because the HSTS policies are never matched due to modified hostnames observed by the victim.

{% hint style="info" %}
A new twist on this attack was released by Jose Selva. He demonstrated spoofing NTP traffic to force the HSTS record to be expired by the victim browser.
{% endhint %}

### Bettercap SSLStrip

#### (for older versions)

```
bettercap --proxy -T 1172.16.0.186 -P POST
```

#### (for newer versions)

```
bettercap
» hstshijack/hstshijack
```
