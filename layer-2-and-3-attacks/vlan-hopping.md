# VLAN Hopping / Double Tagging

## Trunk Port Refresher

![](<../.gitbook/assets/image (278).png>)

#### Trunk ports have access to all VLANs by default and are used to route traffic for multiple VLANs across the same physical link (generally used between switches). Encapsulation can be 802.1Q or ISL

## Dynamic Trunk Protocol (DTP)

### What is DTP?

* Automates ISL/802.1Q trunk configuration&#x20;
* Operates between switches&#x20;
* Does not operate on routers

• DTP synchronizes the trunking mode on link ends

• DTP prevents the need for management intervention on both sides

• DTP state on ISL/1Q trunking port can be set to “Auto”, “On”, “Off”, “Desirable”, or “NonNegotiate”

![](<../.gitbook/assets/image (294).png>)

### DTP Administrative States

#### Administrator configurable trunk states:

![](<../.gitbook/assets/image (288).png>)

## Basic VLAN Hopping

{% embed url="http://www.jay-miah.co.uk/vlan-hopping-concept-attack-example-and-prevention" %}

• A station can spoof as a switch with ISL or 802.1Q signaling(DTP signaling is usually required as well)&#x20;

• The station is then member of all VLANs&#x20;

• Requires a trunking favorable setting on the port (the SANSpaper is two years old)

#### In a basic VLAN hopping attack , the attacker takes advantage of the fact that Dynamic Trunking Protocol (DTP) is enabled by default on most switches. The network attacker configures a system to use DTP to negotiate a trunk link to the switch.&#x20;

#### As a result, the attacker is a member of all the VLANs that are trunked on the switch and can “hop” between VLANs. In other words, the attacker can send and receive traffic on all those VLANs. The best way to prevent a basic VLAN hopping attack is to turn off DTP on all ports, and explicitly configure trunking mode or access mode as appropriate on each port.

![](<../.gitbook/assets/image (282).png>)

here is the VLAN table from the switch console:

![](<../.gitbook/assets/image (292).png>)

The interfaces (G0/0, G0/1) are assigned to VLAN 100 which are the (attacker and victim), and the interface (G0/2) is assigned to VLAN 200.

in order to make the attack successful, the switch has to be on default configuration (in Dynamic Desirable), let’s check the configuration of the attacker’s interface (G0/0):

![](<../.gitbook/assets/image (286).png>)

he switch port is set on Dynamic Desirable thus the VLANs can be negotiated together.

Now we can run the tool (yersinia) in order to enable the TRUNK mode, but before we run the attack let’s see the status of the VLAN:

![](<../.gitbook/assets/image (289).png>)

we can run the tool (yersinia) and choose DTP and then launch attack:

![](<../.gitbook/assets/image (299).png>)

Then choose “enabling trunking” and click OK:

![](<../.gitbook/assets/image (280).png>)

Then we will go back to switch console and we can see that there are packets have been sent as\
shown below:

![](<../.gitbook/assets/image (274).png>)

We will show the VLAN table:

![](<../.gitbook/assets/image (279).png>)

We can see that the interface (G0/0) is set on trunk which means that we can jump other VLANs!

And we can see that all the VLANS are allowed on interface (g0/0):

![](<../.gitbook/assets/image (301).png>)

On Kali, we will add the below commands:

![](<../.gitbook/assets/image (273).png>)

We added a new VLAN interface and we gave it the ID=200. Then we added a new IP and make it up then assign the new created VLAN interface to the eth0.200 interface and make up.

Finally, we can ping the PC-2 that were not accessible and on other VLAN.

![](<../.gitbook/assets/image (285).png>)

So we successfully jumped to the VLAN (200)!



## Double Tagging

{% embed url="https://notsosecure.com/exploiting-vlan-double-tagging" %}

The double-tagging (or double-encapsulated) VLAN hopping attack takes advantage of the way that hardware operates on some switches. Some switches perform only one level of 802.1Q decapsulation and allow an attacker, in specific situations, to embed a second 802.1Q tag inside the frame.

This tag allows the frame to go to a VLAN that the touter 802.1Q tag did not specify. An important characteristic of the double-encapsulated VLAN hopping attack is that it can work even if DTP is disabled on the attackers access port.

![](<../.gitbook/assets/image (290).png>)

• Send double encapsulated 802.1Q frames&#x20;

• Switch performs only one level of decapsulation&#x20;

• Unidirectional traffic only

{% hint style="info" %}
This works even if trunk ports are set to off
{% endhint %}
