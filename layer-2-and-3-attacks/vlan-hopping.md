# VLAN Hopping

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

![](<../.gitbook/assets/image (289).png>)

### DTP Administrative States

#### Administrator configurable trunk states:

![](<../.gitbook/assets/image (284).png>)

## Basic VLAN Hopping Attack

• A station can spoof as a switch with ISL or 802.1Q signaling(DTP signaling is usually required as well)&#x20;

• The station is then member of all VLANs&#x20;

• Requires a trunking favorable setting on the port (the SANSpaper is two years old)

#### In a basic VLAN hopping attack , the attacker takes advantage of the fact that Dynamic Trunking Protocol (DTP) is enabled by default on most switches. The network attacker configures a system to use DTP to negotiate a trunk link to the switch.&#x20;

#### As a result, the attacker is a member of all the VLANs that are trunked on the switch and can “hop” between VLANs. In other words, the attacker can send and receive traffic on all those VLANs. The best way to prevent a basic VLAN hopping attack is to turn off DTP on all ports, and explicitly configure trunking mode or access mode as appropriate on each port.

![](<../.gitbook/assets/image (281).png>)

here is the VLAN table from the switch console:

![](<../.gitbook/assets/image (287).png>)

The interfaces (G0/0, G0/1) are assigned to VLAN 100 which are the (attacker and victim), and the interface (G0/2) is assigned to VLAN 200.

in order to make the attack successful, the switch has to be on default configuration (in Dynamic Desirable), let’s check the configuration of the attacker’s interface (G0/0):

![](<../.gitbook/assets/image (283).png>)

he switch port is set on Dynamic Desirable thus the VLANs can be negotiated together.

Now we can run the tool (yersinia) in order to enable the TRUNK mode, but before we run the attack let’s see the status of the VLAN:

![](<../.gitbook/assets/image (285).png>)

we can run the tool (yersinia) and choose DTP and then launch attack:

![](<../.gitbook/assets/image (294).png>)

Then choose “enabling trunking” and click OK:

![](<../.gitbook/assets/image (279).png>)













