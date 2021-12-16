# Malicious Upgrade/Download

#### Presenting the victim with a malicious payload as a software update package.

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

### Demo

{% embed url="https://www.youtube.com/watch?ab_channel=Unownsec&v=nReKwL93Fnk" %}

## Xerosploit

{% embed url="https://github.com/LionSec/xerosploit" %}

Xerosploit is a penetration testing toolkit whose goal is to perform man in the middle attacks for testing purposes. It brings various modules that allow to realise efficient attacks, and also allows to carry out denial of service attacks and port scanning.

### features:

* Port scanning
* Network mapping
* Dos attack
* Html code injection
* Javascript code injection
* Download intercaption and replacement
* Sniffing
* Dns spoofing
* Background audio reproduction
* Images replacement
* Drifnet
* Webpage defacement and more ...

#### installation

```
git clone https://github.com/LionSec/xerosploit
cd xerosploit && sudo python install.py
sudo xerosploit
```

### Demo

{% embed url="https://www.youtube.com/watch?ab_channel=Neodrix&v=35QUrtZEV9U" %}

## Combine with BeFF and Metasploit

{% embed url="https://sploitech.com/exploiting-tools/how-to-do-advanced-mitm-attacks-with-beef-xerosploit" %}

