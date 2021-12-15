# STP Spoofing

## STP

The spanning tree protocol is designed to detect and prevent loops in the network if there are redundant paths between the switches.

Anyone who can emulate a device with a (lower) root switch identifier (by connecting a new virtual device with a lower priority or using the STP packet generation tool) can partially or completely intercept the virtual network traffic.

&#x20;Typically, an attacker does not have a physical connection with two switches, so the described attack method is hardly possible. However, in wireless networks, the situation is changing, since the cable connection (socket in the office) and the wireless connection (access point) can end on different switches.

### Exploitation

{% hint style="danger" %}
Often this type of attack leads to a denial of service.
{% endhint %}



1. **Send BPDU messages from attacker to force spanning tree recalculations (Impact likely to be DoS)**
2.  #### Send BPDU messages to become root bridge

    The hacker then sees frames he shouldn’t

![](<../.gitbook/assets/image (295) (1) (1).png>)

![](<../.gitbook/assets/image (282).png>)

use yersinia in CLI mode to and select STP from protocol menu. then press 6 to claim root and be the man in the middle:

![](<../.gitbook/assets/image (294) (1).png>)
