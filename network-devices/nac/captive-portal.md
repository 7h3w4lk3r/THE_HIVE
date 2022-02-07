# ⭕ Captive Portal

#### The initial barrier in some networks with inline or OOB management. captive portal changes the VLAN with SNMP post-authentication. sometimes IP and/or MAC is used to validate clients.

### Attack Surfaces

* Targeting captive portal server (web application authentication form)
* Targeting pre-auth services such as DNS and DHCP
* Targeting pre-authenticated client devices (MAC spoof)

## Pre-Auth Client Impersonation

### MAC Spoof

#### use wireshark or other sniffers to find the MAC address of current authenticated devices

now try to impersonate that MAC address:

#### Linux

```
ifconfig eth0 down
ifconfig eth0 hw ether <MAC address>
ifconfig eth0 up
```

#### Windows

```
macshift.exe -i "Wireless Network Connection" <MAC address without colon>
```

#### OSX

```
sudo ifconfig en0 ether <MAC address>
```

{% hint style="info" %}
You find a list of all OUIs in `/usr/local/share/oui.txt` file in linux
{% endhint %}

## Authentication Bypass (Avoiding)

### User-Agent Impersonation

We can use browser plugins such as "user-agent switcher" for firefox to impersonate another device identity.

### TCP Stack Fingerprinting

An additional method used for identifying the OS of clients in a NAC environment is through TCP stack fingerprinting. Primarily used by inline captive portal systems, the gateway can passively monitor TCP traffic to identify characteristics that correlate to the known platform. This technique is similar to that used by the opensource tool "p0f", which examines TCP SYN frames for the following characteristics:

* Initial Time To Live (TTL)
* TCP window size
* Overal frame size of initial TCP SYN frames
* Status of the Dont Fragment (DF) flag
* Value of the maximum segment size
* value of the TCP window scale
* Behavior of the TCP timestamp option
* Status of the selective ACK flag
* Order and presence of TCP flags including NOPs
* Unique quirks for the TCP SYN packet (such as packet data following TCP options)

```
p0f
```

### OS Impersonation

#### Linux

[The fingerprint format](https://github.com/p0f/p0f/blob/master/docs/README#L513-L624):

`sig = ver:ittl:olen:mss:wsize,scale:olayout:quirks:pclass`

#### You will need to spoof the following to change your fingerprint:

* OS TTL: `sudo sysctl net.ipv4.ip_default_ttl=128` (Windows)
* Maximum Segment Size: `sudo sysctl net.ipv4.route.min_adv_mss=1460`
* TCP window size: `sudo sysctl -w net.ipv4.tcp_rmem='8192 87380 4194304' && sudo sysctl -w net.ipv4.tcp_wmem='8192 87380 4194304'`
* Unfortunately, at the `olayout` section you cannot change these settings in Linux. So, you cannot spoof these. The same is true in the `quirks` section. It just so happens that `olayout` "...is one of the most valuable TCP fingerprinting signals.

#### Windows

use the OSfuscate tool from Irongeek, available at http://www.irongeek.com/i.php?page=security/code. OSfuscate uses a simple list of Windows INI formatted profile descriptor files to describe several characteristics of a target TCP/IP stack.

{% embed url="http://irongeek.com/downloads/OSfuscate.3.zip" %}

{% hint style="info" %}
NAC systems do not attempt to perform OS characterization and client checking for each packet; instead, clients are evaluated initially when they connect to the network and at later regular intervals. On Cisco NAC systems, for example, clients are evaluated initially and then as frequently as every 5 minutes, but not less, due to performance limitations of the product.
{% endhint %}

we can craft packets using any arbitrary settings to send for the NAC to use in its evaluation, generating our traffic to appear like an iPad device. Tools such as Scapy make this straightforward, allowing us to send the initial TCP SYN and complete the three-way handshake.

#### scapy script

```python
#!/usr/bin/python
from scapy.all import *
DSTIP="10.10.10.110" # Specify your target where NAC will observe it
SPORT=RandNum(1024,65535)
ip=IP(dst=DSTIP, flags="DF", ttl=64)
tcpopt = [ ("MSS",1460), ("NOP",None), ("WScale",2), ("NOP",None),
("NOP",None), ("Timestamp",(123,0)), ("SAckOK",""), ("EOL",None) ]
SYN=TCP(sport=SPORT, dport=80, flags="S", seq=10, window=0xffff, options=tcpopt)
SYNACK=sr1(ip/SYN)
# Send the packet and record the response as SYNACK
my_ack = SYNACK.seq + 1 # Use the SYN/ACK response to get initial seq. number
ACK=TCP(sport=SPORT, dport=80, flags="A", seq=11, ack=my_ack, window=0xffff)
send(ip/ACK)
data = "GET / HTTP/1.1\r\nHost: " + DSTIP + "\r\ Mozilla/5.0 (iPad; CPU iPad OS 10_0 like Mac OS
X) [...]\r\n\r\n"
PUSH=TCP(sport=SPORT,dport=80, flags="PA", seq=11, ack=my_ack, window=0xffff)
send(ip/PUSH/data)
RST=TCP(sport=SPORT,dport=80, flags="R", seq=11, ack=0, window=0xffff)
send(ip/RST)
```

Using this script, the p0f tool identifies the traffic as an "iOS Apple iPad/iTouch/iPad" device, sufficiently fooling a NAC device into thinking the TCP stack is of an iPad or related device. Once you complete the three-way handshake and send data, the NAC system will pass the client for this TCP fingerprint check, allowing you to disable the local firewall rules we created earlier ("iptables -F") and use your native operating system TCP stack.

### JavaScript OS Validation

identify the native operating system of a client is to insert JavaScript code in an HTTP server's response that queries several browser DOM fields.

#### four fields commonly used for client OS detection:

• navigator.buildID – Used to disclose the build number for the browser; not used by Apple's Safari on the iPad

• navigator.oscpu – Used to disclose the CPU type used on the host; not used by Apple's Safari on the iPad

• navigator.product – Used to disclose the product name; set to "Gecko"

• navigator.productSub – Used to disclose a sub-name to the product field; set to "20030107" on Apple's Safari on the iPad

#### To bypass a NAC system using JavaScript OS validation, we need to manipulate the responses from these fields as well. We could use a different plugin, tampering proxy, or fake the settings with the browser itself to deceive fingerprinting techniques.

{% hint style="info" %}
Firefox allows us to customize the values that are returned from the DOM through JavaScript by creating configuration keys in the format general.XXX.override, where "XXX" is the all-lowercase name of the DOM suffix after "navigator." (For example, to override navigator.oscpu, we can create a key called general.oscpu.override with an arbitrary string value.)

1. Browse to the "about:config" page to access the Firefox configuration key menu.
2. Right-click any key and select New | String.
3. In the "New String Value" dialog, enter the string "general.XXX.override", where "XXX" is the name of the DOM key you wish to manipulate. Click OK.
4. Enter the value for the key to match that of the client you are impersonating. In the example on this slide, the key "general.oscpu.override" is configured with the string value "undefined", matching that of the iPad.
{% endhint %}
