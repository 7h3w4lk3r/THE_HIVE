# VLAN Hopping

## Trunk Port

![](<../../../.gitbook/assets/image (278) (1) (1) (1).png>)

#### Trunk ports have access to all VLANs by default and are used to route traffic for multiple VLANs across the same physical link (generally used between switches). Encapsulation can be 802.1Q or ISL

## Dynamic Trunk Protocol (DTP)

#### The Dynamic Trunking Protocol (DTP) is a Cisco proprietary implementation to allow the switch to determine and negotiate the switchport state as a trunk port using IEEE 802.1Q or Inter-Switch LAN (ISL, a Cisco proprietary trunking protocol) or as an access port.

* Automates ISL/802.1Q trunk configuration
* Operates between switches
* Does not operate on routers

• DTP synchronizes the trunking mode on link ends

• DTP prevents the need for management intervention on both sides

• DTP state on ISL/1Q trunking port can be set to “Auto”, “On”, “Off”, “Desirable”, or “NonNegotiate”

#### If another switch connects to a DTP port, the DTP switch will watch for the presence of 802.1Q or ISL traffic for 30 seconds. When either protocol is discovered, the DTP port will auto-configure to match the trunking configuration, sharing VLAN information with downstream switches. If no 802.1Q or ISL traffic is observed, the switch will default the port to an access port, allowing the user to connect to the default or specified VLAN.

![](<../../../.gitbook/assets/image (294) (1) (1) (1) (1) (1).png>)

### DTP Administrative States

#### Administrator configurable trunk states:

![](<../../../.gitbook/assets/image (288) (1) (1) (1) (1).png>)

#### As an attacker, if we can trick the switch into thinking our connected system is a switch using 802.1Q, then we can trick the switch into configuring the port as a trunk, passing down all VLAN traffic with similar upstream access.

## Basic VLAN Hopping

{% embed url="http://www.jay-miah.co.uk/vlan-hopping-concept-attack-example-and-prevention" %}

• A station can spoof as a switch with ISL or 802.1Q signaling(DTP signaling is usually required as well)

• The station is then member of all VLANs

• Requires a trunking favorable setting on the port (the SANSpaper is two years old)

#### In a basic VLAN hopping attack , the attacker takes advantage of the fact that Dynamic Trunking Protocol (DTP) is enabled by default on most switches. The network attacker configures a system to use DTP to negotiate a trunk link to the switch.

#### As a result, the attacker is a member of all the VLANs that are trunked on the switch and can “hop” between VLANs. In other words, the attacker can send and receive traffic on all those VLANs. The best way to prevent a basic VLAN hopping attack is to turn off DTP on all ports, and explicitly configure trunking mode or access mode as appropriate on each port.

![](<../../../.gitbook/assets/image (282) (1) (1) (1) (1) (1).png>)

here is the VLAN table from the switch console:

![](<../../../.gitbook/assets/image (292) (1) (1) (1) (1) (1).png>)

The interfaces (G0/0, G0/1) are assigned to VLAN 100 which are the (attacker and victim), and the interface (G0/2) is assigned to VLAN 200.

in order to make the attack successful, the switch has to be on default configuration (in Dynamic Desirable), let’s check the configuration of the attacker’s interface (G0/0):

![](<../../../.gitbook/assets/image (286) (1) (1) (1).png>)

he switch port is set on Dynamic Desirable thus the VLANs can be negotiated together.

Now we can run the tool (yersinia) in order to enable the TRUNK mode, but before we run the attack let’s see the status of the VLAN:

![](<../../../.gitbook/assets/image (289) (1) (1) (1).png>)

### Yersinia

we can run the tool (yersinia) and choose DTP and then launch attack.

yersinia can run with ncurses CLI inerface of GUI interface.

to run in CLI mode, in you terminal type in :

```
yersinia -I
```

![](<../../../.gitbook/assets/image (283) (1) (1).png>)

To run in GUI mode type:

```
yersinia -G
```

![](<../../../.gitbook/assets/image (299) (1).png>)

Then choose “enabling trunking” and click OK.

![](<../../../.gitbook/assets/image (280) (1) (1).png>)

in CLI mode press `g` to bring up the protocol menu then choose DTP

![](<../../../.gitbook/assets/image (287) (1) (1) (1).png>)

then press `x` to open panel and press `1` to enable trunking

![](<../../../.gitbook/assets/image (293) (1) (1) (1).png>)

After delivering the DTP message, press `5` to open a status dialog. Here, the status indicates TRUNK/AUTO, revealing a successful attack.

#### Yersinia will monitor network traffic observed and record information such as IP addresses, protocol information, and VLAN settings. After executing the DTP attack, we can let Yersinia continue to monitor the network and build a list of accessible VLANs. To access the list of observed VLANs, addresses, and protocols (such as the example shown in this slide), press "g" to select the protocol mode selection dialog, scroll to select the 802.1Q entry, and press Enter.

![](<../../../.gitbook/assets/image (285) (1).png>)

Then we will go back to switch console and we can see that there are packets have been sent as\
shown below:

![](<../../../.gitbook/assets/image (274) (1).png>)

We will show the VLAN table:

![](<../../../.gitbook/assets/image (279) (1) (1) (1) (1).png>)

We can see that the interface (G0/0) is set on trunk which means that we can jump other VLANs!

And we can see that all the VLANS are allowed on interface (g0/0):

![](<../../../.gitbook/assets/image (301) (1) (1) (1).png>)

On Kali, we will add the below commands:

```
modprobe 8021q
vconfig add eth0 200
ifconfig eth0.200 up
ifconfig eth0.200 10.0.0.6 up
dhclient eth0.200
```

![](<../../../.gitbook/assets/image (273) (1) (1).png>)

We added a new VLAN interface and we gave it the ID=200. Then we added a new IP and make it up then assign the new created VLAN interface to the eth0.200 interface and make up.

Finally, we can ping the PC-2 that were not accessible and on other VLAN.

![](<../../../.gitbook/assets/image (285) (1) (1) (1).png>)

So we successfully jumped to the VLAN (200)!

```
nmap -sS -F -p- 10.0.0.4
```

## Double Tagging

{% embed url="https://notsosecure.com/exploiting-vlan-double-tagging" %}

The double-tagging (or double-encapsulated) VLAN hopping attack takes advantage of the way that hardware operates on some switches. Some switches perform only one level of 802.1Q decapsulation and allow an attacker, in specific situations, to embed a second 802.1Q tag inside the frame.

This tag allows the frame to go to a VLAN that the touter 802.1Q tag did not specify. An important characteristic of the double-encapsulated VLAN hopping attack is that it can work even if DTP is disabled on the attackers access port.

![](<../../../.gitbook/assets/image (290) (1) (1) (1) (1).png>)

• Send double encapsulated 802.1Q frames

• Switch performs only one level of decapsulation

• Unidirectional traffic only

{% hint style="info" %}
This works even if trunk ports are set to off
{% endhint %}

## Voic VLAN Hopping

Another technique to evaluate when evading the NAC system is the option to perform VLAN hopping, particularly in environments where VoIP devices are used to bridge a workstation and a phone over a single network cable. Cisco switches support a special configuration mode where a single switch port can be used to connect a VoIP phone to a voice VLAN, while a second device can connect to the phone to access a different VLAN.

The Cisco VoIP phone effectively becomes a two-port switch, allowing the customer to retain their existing switch density while accommodating the VoIP phones on their network.

To bridge the traffic from the workstation on a different VLAN than the phone, however, the VoIP phone must become a trunk port, if only a limited one, to differentiate between its own traffic and the traffic of the downstream device.

### Exploitation

First of all, we must have our PC connected directly to the VoIP phone that we are going to spoof (obviously, this will be connected to the corresponding switch).

With the successful connection, we will capture a CDP packet. Being directly connected to the Cisco device (VoIP phone), the only package that should reach us is that of the phone itself.

![](<../../../.gitbook/assets/image (288) (1) (1) (1).png>)

With the captured package we verify that it really is the information we need.

![](<../../../.gitbook/assets/image (297) (1) (1) (1).png>)

Once we have the necessary package, we disconnect from the phone and connect directly to the network (that is, because we are going to impersonate the Cisco dispositive, if we leave the device connected, the switch will detect that there are two devices with the same configuration and impersonation will not take effect).

We replicate the device package to make the Cisco switch believe that we are a VoIP device. In addition, these phones send a package every 60 seconds approximately to ensure the connection, so we must do the same to be trusted.

![](<../../../.gitbook/assets/image (300) (1) (1) (1).png>)

We must create a virtual interface by renaming our interface + the VLAN of the Cisco device.

```
vconfig add eth1 29 #29 is the VLAN number of Cisco device
```

We check that it has been created successfully.

![](<../../../.gitbook/assets/image (275) (1) (1) (1) (1).png>)

We disable the interface to modify the MAC and change it for the legitimate phone (we can see this setting directly in phone options).

![](<../../../.gitbook/assets/image (286) (1) (1).png>)

With everything configured, we make a dhclient of the interface to verify that the switch provides us with the IP of the phone.

![](<../../../.gitbook/assets/image (292) (1) (1) (1).png>)

We see how we already have the IP. We only have to verify that we have a connection to the gateway to verify that we are correctly authenticated in the VoIP VLAN.

![](<../../../.gitbook/assets/image (284) (1).png>)

### Automation

#### voiphopper

Automates voice VLAN hopping attack,Listens for CDP to extract voice VLAN number,Creates interface, requests DHCP address.

Includes attack options for Cisco, Avaya, and Nortel switches.

```
voiphopper -c 0 -i eth0
```

{% hint style="info" %}
To use voiphopper, you must install the DHCP client utility "dhclient." Voiphopper will not attempt to create the VLAN sub-interface if the dhclient utility is missing.
{% endhint %}
