# ⭕ Cobalt Strike

Cobalt Strike (CS) is an advanced c2 framework and adversary emulation toolkit that closes the gap between penetration testing tools and advanced threat malware.

{% embed url="https://github.com/martabyte/Red-Team-Ops/blob/main/Red-Team-Ops.md" %}

{% embed url="https://www.google.com/url?sa=t&source=web&cd=&ved=2ahUKEwiG6IbIoo39AhUHDuwKHdiPByEQtwJ6BAgSEAI&url=https%3A%2F%2Fwww.youtube.com%2Fwatch%3Fv%3Dq7VQeK533zI&usg=AOvVaw08QGhtzCysaO_LXY8HIYRI" %}

## <mark style="color:red;">Intro</mark>

#### <mark style="color:orange;">Beacon (C2 Callback Agent)</mark>

* HTTP/S or DNS to egress network
* SMB or TCP for peer-to-peer C2

#### <mark style="color:orange;">Malleable C2 controls...</mark>

* Network traffics
* In-memory content, characteristics and behavior
* Process injection behavior

#### <mark style="color:orange;">Multiple team servers (\*Recommended)</mark>

* <mark style="color:green;">**Staging Server**</mark>
  * Getting initial callbacks
  * Initial privilege escalation + install persistence
  * Expect these servers to get caught!
* <mark style="color:green;">**Long Haul Server**</mark>
  * "Low and Slow" persistent callbacks
  * pass access to post-exploitation as needed
* <mark style="color:green;">**Post-Exploitation Server**</mark>
  * Post-exploitation and lateral movement

## <mark style="color:red;">Beacons</mark>

* <mark style="color:orange;">**HTTP Beacon**</mark>
  * You can add IPv4, IPv6 or Domain (FQDN) for listeners
* <mark style="color:orange;">**HTTPS Beacon**</mark>
  * You can add valid SSL cert
* <mark style="color:orange;">**DNS Beacon**</mark>
  * Edit zone file for a domain you control
  * Create an A record for CS system
  * Create an NS record that points to your CS system FQDN
* <mark style="color:orange;">**SMB/TCP Beacon**</mark>
  * <mark style="color:green;">**Peer-to-peer beacon**</mark>
    * This creates a child process of your initial beacon
  * <mark style="color:green;">**Control over SMB beacon**</mark>
    * `beacon> link [host] [pipe]`
    * `beacon> unlink [host] [pid]`
  * <mark style="color:green;">**Control over TCP beacon**</mark>
    * `beacon> connect [host] [port]`
    * `beacon> unlink [host] [pid]`
* <mark style="color:orange;">**External C2 Beacon**</mark>
  * You can start an External C2 listener on your CS server
  * Use other External C2 to connect to the listener

## <mark style="color:red;">Infrastructure</mark>

### <mark style="color:orange;">Listeners</mark>

* Egress: Beacons out of a network
* Peer-to-peer: Communicates through a parent payload
* Alias: Reference to a payload handler

### <mark style="color:orange;">Payloads</mark>

* <mark style="color:green;">**Stager**</mark>
  * Download a payload --> pass it for execution
  * Less secure
  * More brittle (it can crash)
  * Easier to be detected
* <mark style="color:green;">**Stageless**</mark>
  * No stager. Single file contains all.
  * Most of the CS payloads are now stageless

### <mark style="color:orange;">Redirectors</mark>

* Use Iptables, Socat or other tools to forward traffics to your CS team server.
* Use an Apache or Nginx reverse proxy config
* Use a CDN as a redirector for HTTPS traffic



* <mark style="color:green;">**HTTP Redirector Example**</mark>
  * Start a team server (main.bigb0ss.com)
  * On the redirector box (redir.bigb0ss.com)
    * `apt-get update && apt-get install socat`
    * `screen -S socat_redir`
    * `socat TCP4-LISTEN:80,fork TCP4:main.bigb0ss.com:80`



*   <mark style="color:green;">**CDN Redirector Example**</mark>

    * Use valid SSL certificate
    * Allows HTTP Post and HTTP GET verbs (\*Malleable C2)
    * Consider using HTTP-GET only C2 (\*Malleable C2)
    * Be aware of transformed requests (\*Malleable C2)
    * Disable all cache options : This is because we want the CDN to not cache our contents and callback to us every time the target calls back to our C2.



## <mark style="color:red;">Domain Fronting</mark>

Domain fronting is basically making the C2 traffic from the target system that looks like going into the highly trusted domain "T" but actually making it to our C2. Helps bypassing egress controls or making the C2 traffic blended into normal/legitimate traffics.

### <mark style="color:orange;">CDN Domains</mark>

Reason for using CDN domains are that they are highly trusted, and it is hard to block all the trusted CDN domains. If blocked, people cannot access to some legitimate websites.

:warning: **HTTP Traffic Considerations :** For plain HTTP traffic, if the target organization is using a proxy server, it can inspect the HTTP GET request host and its "Host:" header value. If the request host and the header value are not matching, it will overwrite the "Host:" header to point it to the GET request host. This is called "Normalization." This will break the Domain Fronting.

:warning: **HTTPS Traffic Considerations :** For plain HTTPS traffic, the proxy server is only able  to see the "CONNECT Domain:443" URL and unable to  inspect the encrypted the headers. But many companies  can do MitM-SSL between the proxy server and the SSL  connection, so they may catch the domain fronting attempts. (However, consider that like finance and healthcare organizations would not do the MitM-SSL because they don't want to see people's PII date accidentally.)

:warning:  **Server Name Indication ("SNI") Considerations :** Some CDN providers now check the "SIN:" value within the HTTP/S request, and if that is different than "Host:" header, it flags as malicious. It is one of the reasons why people say Domain Fronting is dead now, but not always.



## <mark style="color:red;">Malleable C2 Profile</mark>

* Network traffic modifications
* In-memory content, characteristics and behaviors
* Process injection behaviors
* Testing Profile: ./c2lient \[profile]

{% embed url="https://blog.zsec.uk/cobalt-strike-profiles/" %}

{% embed url="https://posts.specterops.io/a-deep-dive-into-cobalt-strike-malleable-c2-6660e33b0e0b" %}

{% embed url="https://hstechdocs.helpsystems.com/manuals/cobaltstrike/current/userguide/content/topics/malleable-c2-extend_process-injection.htm?cshid=1053" %}

### <mark style="color:orange;">Tips</mark>

* Don't use public example in production
* Don't allow an empty http-get > server > output response.
* Use 'prepend' to prepend junk data
* Use 'transform' to mask/randomize data
* Change URIs and use 'prepend' to mask IOCs in http-stager block. (If you are going to allow staging)
* Use http-config block to standardize server headers and  order of the header for all HTTP server responses.
* Use plausible useragent value for target network.
* Use HTTP Get-Only C2 for difficult egress situations.



## <mark style="color:red;">C2 Considerations</mark>

### <mark style="color:orange;">HTTP/S Proxy</mark>

CS's HTTP/S payloads are built with WinINet libraries, so  it has default features of automatic NTLM authentication  for domain users; however, if you are calling as SYSTEM this may fail. Be aware that WinINet library may have TLS limitation incompatible with your redirectors.

### <mark style="color:orange;">Network Security Monitoring</mark>

Use an Apache, Nginx or a CDN as a redirector, It smooths CS-specific indicators, better JA3S fingerprint, etc.

JA3 (JA3S - server) technology indicates the value of the applications; therefore, using a redirector as a legit software like Apache or Nginx is really important. (You don't want your redirector to be looking like a random Java on Debian running) -> One of the biggest reasons why you should use redirectors.

Host redirectors on different VPS providers. Domains are better with age and categorization.

Do not use IPv4 addresses for C2. Have a valid SSL certificate

## <mark style="color:red;">Communication Path Do/Don't</mark>

:green\_circle: **Do:**

* Egress from plausible user processes on workstations
* Use SMB Beacon from workstations to servers
* Use built-in Windows tools and APIs to find targets

:red\_circle: **Don't:**

* Servers should not egress
* SMB beacon from server to workstations is weird
* Scan to find targets

## <mark style="color:red;">Red/Blue Tips for C2 Detection</mark>

:red\_circle: **RED:**

* Use valid SSL cert
* Use redirectors
* Only allow HTTP/S connections from redirectors
* Firewall port 50050 and access via SSH tunnel
* Host content on your redirectors
* Do not use DNS C2
* Use DNS C2 as low\&slow fallback option only
* Set 'dns\_stager\_prepend' and 'dns\_stager\_subhost'
* Change 'dns\_idle' in profile. Avoid 'mode dns' as this will send bogon responses
* Set 'dns\_mas\_txt' to limit TXT length. Set 'maxdns' to limit hostname length.

:blue\_circle:**Blue:**

* Check for CS default SSL cert
* Check for port 50050 open
* Use Split-Split DNS
* Empty index page, 404, text/plain Content-Type
* Monitor volume of DNS requests
* Check for CS DNS C2 IOCs
* Check for bogon IP (0.0.0.0)
* Check for length of request hostnames
