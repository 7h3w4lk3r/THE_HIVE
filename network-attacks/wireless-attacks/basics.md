# ⭕ Basics

Get Wireless interface status:

```
iwconfig
```

Get status of driver:

```
dmesg | grep 2x00
```

Get  information on your wireless NIC and it's driver

```
iw list | less 
```

Get a list of the wireless networks around you

```
iw dev wlan0 scan | grep SSID
```

Get a list of channel numbers and their corresponding frequencies

```
 iwlist wlan0 frequency 
```

Get a list of Networks around you and the channel that they are on

```
 iwlist wlan0 scanning | egrep "ESSID|Channel"
 iw dev wlan0 scan | egrep "DS\ Parameter\ set|SSID"
```

## <mark style="color:red;">Create a VAP</mark>

virtual access point that is in monitor mode

```
iw dev wlan0 interface add mon0 type monitor
 ifconfig mon0 up 
```

now type ifconfig, and you should see an interface called mon0.

now lets double check that you are actually IN MONITOR mode:

```
tcpdump -i mon0 -s 65000 -p 
```

If you see the IE beacon and privacy mode etc...then you are successfully in monitor mode.

Now to delete the VAP if you want to

```
iw dev mon0 interface del
```

## <mark style="color:red;">Scan for APs</mark>

<mark style="color:green;">Visible APs:</mark>

```
sudo iwlist wlan0 scanning
```

```
kismet -c wlan0
```

<mark style="color:green;">Hidden APs:</mark>

If user stop the broadcasting of wireless network (SSID) from gateway then inbuilt wifi scanner can't detect the particuler ssid|wireless network.

```
airmon-ng start wlan0
airodump-ng wlan0
```

up to this point we should see the network BSSID address in airodump output. we are interested in the name of wireless network and ssid name display only if the client re-authenticates to the hidden ssid network.&#x20;

Here we will use the aireplay-ng tool to deauthenticate all the connected client from wireless hidden network.so when de-authenticated client try to re-authenticate,we get the ssid name of hidden network.

Now open a new terminal window and leave airodump-ng running.

```
aireplay-ng -0 30 -a BC:F6:85:BF:4F:70 mon0
```

the SSID should now be visible in airodump-ng terminal.

### <mark style="color:orange;">Setting Monitoring Mode</mark>

#### <mark style="color:green;">Manual</mark>

```
iw dev wlan0 interface add mon0 type monitor
ifconfig mon0 up
iw dev mon0 set channel 1
iw dev mon0 info

iw dev mon0 del
```

#### <mark style="color:green;">airmon-ng</mark>

```
# list interfaces
airmon-ng

# put interface in monitoring mode
airmon-ng start/stop wlan0\
iw dev wlan0 del
```

## <mark style="color:red;">Sniffing</mark>

### <mark style="color:orange;">Managed Mode Sniffing</mark>

In managed mode, we can capture traffic that is formatted according to the IEEE 802.3 specification but only when it is associated with the wireless network. The traffic that is captured is limited to data packets traversing the wireless network, excluding all wireless network management and control packets.

{% hint style="info" %}
The interface is in managed mode
{% endhint %}

#### <mark style="color:green;">tcpdump</mark>

```
tcpdump -neti wlan0
```

#### tcpdump filters for wireless frames:

| Type |              |     |          |
| ---- | ------------ | --- | -------- |
| mgt  | assoc-req    | ctl | ack      |
| mgt  | reassoc-req  | ctl | rts      |
| mgt  | reassoc-resp | ctl | cts      |
| mgt  | probe-req    | ctl | data     |
| mgt  | probe-resp   | ctl | null     |
| mgt  | beacon       | clt | qos-data |
| mgt  | disassoc     | ctl | cf-ack   |

#### refer to [this section](../sniffing/tcpdump.md) for more tcpdump options.

#### <mark style="color:green;">wireshark filter</mark>

* **!wlan.fc.type\_subtype == 8 :** This filter will list all the frames that do not have an 802.11 frame control subtype value of 8. This filter will exclude all the beacon frames from a wireless packet capture.
* **!wlan.fc.protected == 1** : This filter will list all the frames that do not have the WEP bit (or privacy bit) set. This is useful for identifying unencrypted wireless traffic.
* **wlan.bssid == 00:e0:63:82:19:c6 :** This filter will display only packets that have the specified BSSID. By changing the BSSID value, this filter will be useful for excluding any traffic received from other nearby access points on the same channel.
* **frame contains ORA-**   : This filter will search the entire packet for any instances of the string "ORA-". Note that this string is case sensitive and will not match instances of "ora-" or "Ora-". This filter is useful for identifying unencrypted Oracle traffic on a network.
* **tcp or udp or arp or isakmp** :  This filter does not use the typical expression logic for traffic filtering. Instead, it will discard all fields that are not of the type TCP, UDP, ARP, or ISAKMP. This filter is useful for extracting these specific upper-layer protocols and can be modified to add or subtract specific protocol fields.
* **!(wlan.bssid == 00:10:e7:f5:c3:1f or wlan.bssid == 00:60:1d:f0:47:39)**  : This filter shows all traffic that is not from either of the identified BSSIDs. This type of filter is useful for excluding traffic from "known secure" networks, looking to identify any traffic from other wireless networks.

{% embed url="https://www.wifi-professionals.com/2019/03/wireshark-display-filters" %}

### <mark style="color:orange;">Monitor Mode Sniffing</mark>

monitor mode sniffing captures and presents the raw 802.11 traffic without converting it to the 802.3 format. While in monitor mode, the network card will capture traffic sent to any wireless network for a given channel, including the data frames, management frames, and control frames.

{% hint style="info" %}
The interface is in monitoring mode before starting the sniffer
{% endhint %}

```
airmon-ng start wlan0
```

```
tcpdump -neti mon0
```
