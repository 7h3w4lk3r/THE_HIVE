# ⭕ Layered Security

## <mark style="color:blue;">Layer 1</mark> &#x20;

### <mark style="color:yellow;">(Physical Inspection/Access)</mark>

#### <mark style="color:red;">Threats</mark>

* Rogue Device
* Bad USB
* Pentest Dropbox
* HID Attacks
* Physical Damage

<mark style="color:green;">Mitigations</mark>

* Cabinets
* Closets
* Physical Authentication & Authorization

### <mark style="color:yellow;">Wireless (802.11)</mark>

#### <mark style="color:red;">Threats</mark>

* Dual-homed devices (2 or more interfaces)
* Protocol Attacks
* BYOAP (bring your own access point)

#### <mark style="color:green;">Mitigations</mark>

* WIPS (wireless Intrusion Prevention System)
* Disable Obsolete Protocols (WEP)
* Disable Flawed Control Mechanisms (WPS)
* Enable PMF (Protected Management Frames) if possible - available in 802.11w to block spoofing, deauth, disass and replay attacks
* Guest Management (no auth for guests, internet access only, WPA2-PSK)
* Station/Client Isolation (clients cant access other clients on the same AP)
* WPA2 Enterprise (use unique encryption key for each client and authenticate with RADIUS servers)

### <mark style="color:yellow;">Wireless (RFID badges)</mark>

<mark style="color:red;">**Threats**</mark>

* Cloned Cards/Tags

<mark style="color:green;">Mitigations</mark>

* Use cards that support rolling codes (code change on each use) or challenge-response&#x20;
* Use protective sleeves if using old RFID cards

## <mark style="color:blue;">Layer 2</mark>

### <mark style="color:yellow;">Switches</mark>

<mark style="color:red;">**Threats**</mark>

* MAC flooding
* 802.1Q and ISL Tagging
* Double Encapsulated 802.Q / VLAN hopping
* ARP Poisoning
* Private VLAN attack
* VLAN Tunneling Protocol Attack
* Multicast Brute Force
* Spanning-tree Attack
* Random Frame Stress
* Rogue DHCP
* DHCP Starvation
* NTP Amplification
* NTP Spoofing
* STP Spoofing
* Sniffing

<mark style="color:green;">**Mitigations**</mark>

* Disable CDP to prevent credential spoofing
* Setup port security to prevent MAC flood or hardcode system MAC address
* Disable unused ports, services, protocols, interfaces, etc.
* Setup MAC limitation and sticky MAC address
* DHCP Snooping
* Dynamic ARP Inspection (DAI)
* ARP IDS (arpwatch)
* Configure trusted interface for DHCP
* Use private VLANs
* use SSHv2
* Enable logging
* Use Cisco type 8 or 9 password hashes
* Configure banner correctly
* Always use SNMP v3 with secure community strings

### <mark style="color:yellow;">Routers</mark>

#### <mark style="color:red;">Threats</mark>&#x20;

* DoS
* IP spoof
* IP Source Routing
* ICMP Flood
* Smurf Attack
* Routing Table Poisoning
* IPv6 Router Advertisement
* Unauthorized Routing Updates
* Wormhole Attack (Unauthenticated Tunneling)

#### <mark style="color:green;">Mitigations</mark>

* Cisco Autosecure
* DISA STIGs
* CIS Cisco Benchmark
* Use audit tools (Nipper Studio, nipper-ng)
* Set logon filters on the outermost external router
* Block non-routed IPs

### <mark style="color:yellow;">IPv6</mark>

#### <mark style="color:red;">Threats</mark>

* IPv6 MitM
* DHCPv6 Takeover
* Neighbor Impersonation
* NDP Spoofing

#### <mark style="color:green;">Mitigations</mark>

* Dynamic ARP Inspection
* Secure Neighbor Discovery
* Private VLANs
* Port Security
* 802.1x
* Use IPsec if possible
* Block protocol 41 to prevent IPv6 tunneling (Cisco IOS ACL)
* Use rogue advertisement (RA) guard

## <mark style="color:blue;">Layer 7</mark>

### <mark style="color:red;">Application Proxy</mark>

![](<../../.gitbook/assets/image (10).png>)

#### <mark style="color:orange;">Forward Proxy</mark> : remote access through proxy, hides the identity of client.

* <mark style="color:green;">Web Proxy</mark>
  * SSL Inspection
  * Block sites by categories
  * Website whitelisting
  * Authentication
* <mark style="color:green;">SMTP Proxy (spam application)</mark>
  * per-email encryption
  * modify/auto-route mail (add custom header/footer or remove attachments)
  * anti-spam/anti-spoof (SPF/DKIM/DMARK)
  * sender authentication
  * rate limiting
  * sender blocking

#### <mark style="color:orange;">Reverse Proxy</mark> : for security analysis, hides the identity of server (WAF, three-tier model, etc.)

{% embed url="https://www.ibm.com/docs/en/secure-proxy/6.0.1?topic=SS6PNW_6.0.1/com.ibm.help.ssp.overview.doc/ssp_overview_architecture.html" %}

#### <mark style="color:orange;">Placement</mark>

* DMZ proxy (between DMZ and internal network)
* Internal proxy (betwwen clients and internet)
* VPN access (for remote connection)
* Cloud proxy (systems directly connect to cloud infrastructure)

### <mark style="color:red;">WAF</mark>

Web Application Firewall (proxy) : strong focus on mitigating OWASP Top 10.

<mark style="color:orange;">**Capabilities**</mark>

* SSL offloading (client connects to reverse proxy with SSL/TLS and reverse proxy connects to web server with http)
* content decoding&#x20;
* HTTP attack vector mitigations
* virtual patching (mitigation without recoding the web app)
* CAPTCHA & rate limiting
* add HSTS header
* error page control
* dynamic action based on risk level
* dynamic traffic routing for load balancing

#### <mark style="color:orange;">Deployment:</mark>

* **Automatic Learning** (traffic goes through WAF for a period of time so it can learn the patterns, after learning can flip to deny)
* **manual** (all settings are manually tuned, many templates are available based on OS, programming language and back-end database )

### <mark style="color:red;">Firewall</mark>

#### <mark style="color:orange;">Internal Network Design</mark>

Break up internal network into separate segments based on :&#x20;

* Business & regulation requirements
* Criticality of assets
* Threats
* Risk appetite

#### <mark style="color:orange;">Segmentation Principles</mark>

classify systems and data in different levels (tiers):

* tier 1 - critical components (DCs, exchange server, etc.)
* tier 2 - internal systems
* tier 3 - external facing data-providing systems

{% hint style="info" %}
gateways are the control points with egress/ingress inspection and ACL.
{% endhint %}

#### <mark style="color:orange;">Best Practices:</mark>

* Define port-based rules first
* Block geolocations
* Authenticate all outbound traffics
* Use both host-based and network-based

### <mark style="color:red;">DMZ</mark>

DMZ is usually implemented in one of these models:

* #### <mark style="color:green;">Three Legged DMZ Model (Single Firewall) :</mark> **uses a single firewall with at least three network interfaces to make the architecture that holds a DMZ.**
* #### <mark style="color:green;">Dual Firewall DMZ Model :</mark> the DMZ is placed between 2 firewalls and traffix is filtered in both sides.

{% hint style="info" %}
In case of having multiple systems/servers in the DMZ, its highly recommended to separate them or split them into multiple DMZs (or trust zones).

In DMZ, segmentation is done using VLANs, VRFs or physical separation.&#x20;
{% endhint %}

![](<../../.gitbook/assets/image (20).png>)

{% embed url="https://www.networkdefenseblog.com/post/design-scenario-2-dmz-design" %}

### <mark style="color:red;">DMZ + Active Directory</mark>

In case of using Active Directory connected servers in the DMZ (such as a public website or domain), the DMZ should not be directly connected to primary/root domain controllers. in this scenario, a read-only domain controller (RODC) is placed in DMZ for authentication and authorization.

{% hint style="info" %}
#### Services Like LDAP and SMB network shares shouldn't be visible from the DMZ point of view. for better security and easier management, use deferent domains for public-facing and internal use.
{% endhint %}

![](<../../.gitbook/assets/image (51).png>)

## <mark style="color:red;">IDS vs IPS Design</mark>

#### IPS should be implemented in in-line mode so that all the traffic can pass through and be analyzed.

#### IDS should be implemented out-of-band with a network tap or port mirroring to prevent speed drop.

![](<../../.gitbook/assets/image (9).png>)
