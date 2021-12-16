# Malicious Upgrade

#### Replacing / Presenting the victim with a malicious payload as a software update package

## IRS-Evilgrade

{% embed url="https://github.com/infobyte/evilgrade" %}

Evilgrade is a modular framework that allows the user to take advantage of poor upgrade implementations by injecting fake updates. It comes with pre-made binaries (agents), a working default configuration for fast pentests, and has it's own WebServer and DNSServer modules. Easy to set up new settings, and has an autoconfiguration when new binary agents are set.

#### This framework comes into play when the attacker is able to make hostname redirections (manipulation of victim's dns traffic), and such thing can be done on 2 scenarios:

**Internal scenery:**

* Internal DNS access
* ARP spoofing
* DNS Cache Poisoning
* DHCP spoofing
* TCP hijacking
* Wi-Fi Access Point impersonation

**External scenery:**

* Internal DNS access
* DNS Cache Poisoning

#### installation

```
apt install irs-evilgrade
evilgrade
```

#### start/stop web and DNS server

```
evilgrade> start
evilgrade> stop
```

#### list available modules

```
evilgrade>show modules
```

#### Configure a specified module

```
evilgrade>conf sunjava
evilgrade(sunjava)>
```

#### Show all VirtualHosts.&#x20;

VirtualHost field contains the domains that our webserver is going to emulate for us.

```
evilgrade>show vhosts
```

#### Show options of current module.&#x20;

**agent:** This is our fake update binary, we have to set the path to where it's located or implement a dynamic fake update binary generation (see ADVANCED).

```
evilgrade(sunjava)>show options
```

#### Show status and victims logs

```
evilgrade>show status
```

## Demo

{% embed url="https://www.youtube.com/watch?ab_channel=Unownsec&v=nReKwL93Fnk" %}
