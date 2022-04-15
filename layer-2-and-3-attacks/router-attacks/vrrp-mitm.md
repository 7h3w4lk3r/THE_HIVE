# 🔧 VRRP MitM

## <mark style="color:red;">VRRP</mark>

In comparison to HSRP, the Virtual Router Redundancy Protocol (VRRP) is a standards-based protocol described in RFC 3768 and augmented in RFC 5798 for IPv6 networks. The operation of VRRP is similar to HSRP, where two or more routers share responsibility for a virtual IP address using the MAC address`00:00:5e:00:01:XX` (where "XX" is the VRRP group). The multicast group 224.0.0.18 is used by the master to send keep-alive messages to other standby devices.

#### Unlike HSRP, VRRP does not use UDP as an IP payload, instead using IP protocol 112. An 8-bit priority field is used to identify the order in which the routers in the VRRP group take over responsibility for the virtual IP address.

Unlike HSRP, VRRP does not include any authentication or integrity checks. As such, all configurations of VRRP are vulnerable when an attacker observes VRRP keep-alive traffic on the LAN. However, Yersinia does not support a VRRP MitM attack. Fortunately, alternative attack tools are available.
