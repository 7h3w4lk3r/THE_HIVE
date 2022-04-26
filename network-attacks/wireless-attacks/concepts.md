# ⭕ Protocol Concepts

## <mark style="color:red;">Standards</mark>

the access point that sends out the radio frequency (RF) signal is known as the AP. These APs are capable of sending out signals (between 2.4 and 5 Ghz) that comply with a number of different standards. These standards are known as 802.11a, 802.11b, 802.11g, and 802.11n. In the very near future, we'll see a new standard that's tentatively named 802.11ac.

The following table lists the main 802.11 protocols along with some of their properties:

![](<../../.gitbook/assets/image (24) (1) (1) (1).png>)

These standards are generally backwardly compatible, so that a wireless n adapter will also be able to pick up g and b signals. We will focus upon the most widely used of these standards— b, g, and n.

### <mark style="color:orange;">IEEE 802.11</mark>

The original IEEE 802.11 standard, release in 1997, defined the rates of 1 or 2Mbit/s and can be used with either infrared, although it was never implemented, or via radio frequencies in Direct-Sequence Spread-Spectrum (DSSS) and Frequency Hopping Spread- Spectrum (FHSS).

IEEE 802.11 also defined the Carrier Sense Multiple Access with Collision Avoidance (CSMA/CA) as the medium access method. In CSMA, a station intending to send data on the medium has to listen for a predetermined amount of time to ensure that no other system is transmitting at the same time. In CSMA/CA, the system that intends to send data first sends a signal on the network telling all other stations not to transmit, and only then does it send its data. In addition to CSMA/CA, Request to Send/Clear to Send (RTS/CTS) can also be used to avoid collisions.

### <mark style="color:orange;">IEEE 802.11b</mark>

The IEEE 802.11b amendment adds Complementary Code Keying (CCK) coding to the standard that can provide 5.5 and 11Mbit/s rates on the 2.4GHz band (2.4GHz – 2.485GHz) and divides this band into 14 overlapping channels. Each of these channels has a width of 22MHz around the central frequency.

The following table shows the relationship between each channel number and its corresponding frequency:

![](<../../.gitbook/assets/image (14) (1) (1).png>)

A quick calculation will show that it’s only possible to have 3 non-overlapping channels and channel availability is dictated by the local standards of each country or region. For example:

• USA – Uses channels 1 to 11 (2.412 GHz – 2.462 GHz)

• Europe – Uses channels 1 to 13 (2.412 GHz – 2.472 GHz)&#x20;

• Japan – Uses channels 1 to 14 (2.412 GHz – 2.484 GHz)

### <mark style="color:orange;">IEEE 802.11a</mark>

IEEE 802.11a was released around the same time as 802.11b but due to a lack, and high price, of existing hardware, it did not have much success. 802.11a uses the 5 GHz band which has 2 major advantages over the 2.4 GHz band used by 802.11b:

* The 2.4 GHz band is extremely crowded with other devices such as cordless phones, Bluetooth devices, and even microwave ovens.
* The 5 GHz band has far more channels available and they do not overlap like those in the 2.4 GHz band.

IEEE 802.11a uses Orthogonal Frequency-Division Multiplexing (OFDM) for its signal modulation and provides a maximum transfer rate of 54Mbit/s. The allowed frequencies can vary depending on your location but in general the 5.15-5.35 GHz range is for indoor use with the 5.7-5.8 GHz range being allocated for outdoor use.

### <mark style="color:orange;">IEEE 802.11g</mark>

IEEE 802.11g uses the same signal modulation as 802.11a but on the 2.4 GHz frequency, resulting in the same speed rates. The signal range is slightly better than 802.11a and it is backwards compatible with IEEE 802.11b. 802.11g will fall back to lower rates when a 802.11b device connects and can switch to CCK (and other modulations), thus reducing global network speed.

### <mark style="color:orange;">IEEE 802.11n</mark>

Work began on IEEE 802.11n in 2004 with the aims of improving transfer rates and providing more range on 2.4 GHz and 5 GHz networks. After 2 years of work, the first draft was released allowing speeds up to 74 Mbit/s with the 2 nd draft being voted on in 2007 allowing speeds up to 300 Mbit/s. Finally, in 2009, the final release of 802.11n was completed.

The speed increase in IEEE 802.11n is due in large part to its use of Multiple-Input Multiple-Output (MIMO) communications technology. In short, MIMO uses multiple antennas, each with its own transmitter and receiver and exploits the multipath radio wave phenomenon, where the signal bounces on all objects such as walls, doors, etc. 802.11n allows for the use of up to 4 antennas resulting in more streams being sent and received and therefore, a much better transfer rate. The channel width can be 40 MHz instead of 20 MHz, thus doubling the data rate.

There is also a new mode called Greenfield mode that introduces a new preamble for 802.11n only whereby only devices operating in 802.11n will be “allowed” on the network.

## <mark style="color:red;">Wireless Operating Modes</mark>

There are 2 main wireless operating modes:

* Infrastructure
* Ad-Hoc

In both modes, a Service Set Identifier (SSID) is required for network verification. In infrastructure mode, the Access Point (AP) sets the SSID whereas in ad-hoc mode, the Station (STA) that is creating the network sets it.

The AP broadcasts the SSID in beacon frames approximately 10 times per second and the client, when connecting to a wireless network, also advertises the SSID. These basic features are used by wireless sniffers to identify network names and gather other interesting pieces of information.

### <mark style="color:orange;">**Infrastructure Network**</mark>** **<mark style="color:green;">****</mark>&#x20;

&#x20;<mark style="color:green;"></mark> In infrastructure mode, there is at least one AP and one station, which together form a Basic Service Set (BSS). The AP is most commonly connected to a wired network, which is called a Distribution System (DS).

An Extended Service Set (ESS) is a set of 2 or more wireless APs connected to the same wired network, defining a single logical network segment.

{% hint style="info" %}
On Linux-type operating systems, acting as a STA is usually called “Managed” mode and when acting as an AP, it is usually referred to as “Master” mode.
{% endhint %}

![](<../../.gitbook/assets/image (34) (1) (1) (1).png>)

### <mark style="color:orange;">Ad-Hoc Mode</mark>&#x20;

An ad-hoc network, also known as an Independent Basic Service Set (IBSS), consists of at least 2 STAs communicating without an AP. This mode is also called “peer to peer mode”. In an ad-hoc network, one of the participating stations takes on some of the responsibilities of an AP such as:

* Beaconing
* Authentication of new clients joining the network

In ad-hoc mode, the STA taking on the responsibilities of the AP does not relay packets to other nodes like an AP does.&#x20;

![](<../../.gitbook/assets/image (46) (1) (1).png>)

### <mark style="color:orange;">Wireless Distribution System</mark>

A Wireless Distribution System (WDS) is similar to a standard DS but is done via wireless and APs communicate with one another. WDS has 2 connectivity modes:

* Wireless Bridging – Only allows WDS APs to communicate with each other
* Wireless Repeating – Allows both stations and APs to communicate with each other&#x20;

![](<../../.gitbook/assets/image (47) (1) (1).png>)

### <mark style="color:orange;">Monitor Mode</mark>&#x20;

Monitor mode is not really a wireless mode but it is especially important in attacking wireless networks. In a nutshell, Monitor mode allows a wireless card to “monitor” the packets that are received without any filtering. Monitor mode is essentially the “promiscuous mode” equivalent for wireless. When using some wireless drivers, this mode allows for the sending of raw 802.11 frames. Airodump-ng, Aireplay-ng, and many other wireless tools require that the adapter be placed in monitor mode in order to operate.

### <mark style="color:orange;">Master mode</mark>

In master mode, a wireless card acts as if it were an access point. In many cases, the same wireless card that is used for client systems is also used for the access point itself, relying on different driver functionality to handle the demands of an access point.

## <mark style="color:red;">802.11 MAC Frame</mark>

![](<../../.gitbook/assets/image (11) (1) (1) (1).png>)

### <mark style="color:orange;">Header</mark>

<mark style="color:green;">Frame Control :</mark>&#x20;

* **Protocol Version:** Provides the version of the 802.11 protocol used. This value is currently 0.
* **Type and Subtype:** Determines the function of the frame. There are 3 different frame type fields: control (value: 1), data (value: 2), and management (value: 0). There are multiple subtype fields for each frame type and each subtype determines the specific function to perform for its associated frame type. More about this later.
* **To DS and From DS:** Indicates whether the frame is going into or exiting the distribution system.
* **More frag:** Indicates whether more fragments of the frame are to follow this one.
* **Retry:** Indicates that the frame is being re-transmitted.
* **Power Mgmt:** Indicates whether the sending STA is in active mode (value: 0) or power-save mode (value: 1).
* **WEP:** Indicates whether or not encryption and authentication are used in the frame.
* **Order:** Indicates that the frame is being sent using the Strictly-Ordered service class. This field is usually not set.

<mark style="color:green;">Duration/ID :</mark>&#x20;

Depending on the frame type, this field can have to different meanings:

* Power-Save Poll (type: 1, subtype: 10): Station Association Identity (AID)
* Other: Duration value used for the Network Allocation Vector (NAV) calculation.

The following table represents the different cases of these addresses, depending on the From/To DS bits:

![](<../../.gitbook/assets/image (1).png>)

The first case in the table is IBSS mode. The FromDS and ToDS bits are not set as is the case when 2 STAs communicate with one another. The other 3 cases are in infrastructure mode:

* Case 2: Only the FromDS bit is set – when the AP talks to the STA.&#x20;
* Case 3: Only the ToDS bit is set – when the STA talks to the AP.&#x20;
* Case 4: Both bits are set in the WDS mode – when one AP talks to another.

{% hint style="info" %}
The 4 th address field only exists in WDS mode.
{% endhint %}

<mark style="color:green;">Sequence Control :</mark>

This field consists of 2 sub-fields used to recognize frame duplication:

* **Sequence Number (12 bit):** Indicates the sequence number of each frame. The sequence number is the same for each frame sent for a fragmented frame. The value range for this field is 0-4095; when it reaches 4095, the next sequence will be 0.
* **Fragment Number (4 bit):** Indicates the number of each fragment of a frame sent. The value range for this field is 0-15.

<mark style="color:green;">Data :</mark>

The data field can contain up to 2324 bytes of data. The maximum 802.11 MAC Service Data Unit (MSDU) is 2304 and the different encryption methods each add their own overhead:

* WEP: 8 bytes – Data Field: 2304 + 8 = 2312 bytes
* TKIP (WPA1): 20 bytes – Data Field: 2304 + 20 = 2324 bytes
* CCMP (WPA2): 16 bytes – Data Field: 2304 + 16 = 2320 bytes

<mark style="color:green;">FCS :</mark>

The Frame Check Sequence (FCS) is the Cyclic Redundancy Check (CRC) of the current wireless frame. A CRC is performed over all previous fields to generate the FCS. When received at the destination, the frame FCS is re-calculated and if it is identical to the one received, then the frame was received without errors.

<mark style="color:green;">Control Frames :</mark>

Wireless control frames are short messages that tell devices when to start or stop transmitting and whether a connection failure occurred. The following table can help you remember the different types of control frames:

![](<../../.gitbook/assets/image (29) (1) (1) (1) (1).png>)

<mark style="color:green;">PS-Poll :</mark>

Wireless adapters can be placed in power-saving mode (nearly off) to increase battery life. When a station is in power-save mode, the AP buffers the traffic destined for it. The AP uses a Traffic Information Message (TIM) to inform the station that it has some data waiting for it and transmits it in beacon frames. When a station finds its AID in the TIM map, it uses PS-Poll frames to request the buffered frames from the AP. Each frame must be ACK’d before being removed from the APs buffer.

<mark style="color:green;">RTS/CTS :</mark>&#x20;

RTS/CTS is a supplement to the CSMA/CA mechanism that helps in reducing collisions. It adds overhead to the wireless communication, as additional packets have to be added to the beginning of the communication.

![](<../../.gitbook/assets/image (15) (1).png>)

we assume that Node 1 wants to communicate with Node 2. Node 2 can be either an AP or a STA.

1. Node 1 sends a “Request to Send” to Node 2.
2. If there was no collision and the request is accepted, Node 2 sends a “Clear to Send” to Node 1 telling it to proceed.
3. Node 1 sends its data.
4. The data is ACK’d by Node 2 if the data was received. Nothing is sent if it fails.

### <mark style="color:orange;">RTS and CTS Frames</mark>

An RTS frame has a length of 20 bytes:

![](<../../.gitbook/assets/image (12) (1) (1) (1).png>)

A CTS frame has the same length (14 bytes) and structure as an ACK frame:

![](<../../.gitbook/assets/image (51) (1) (1) (1) (1).png>)

### <mark style="color:orange;">Management Frames</mark>

Management frames are used to negotiate and control the relationship between access points and stations. The following table outlines the different types of wireless management frames.

![](<../../.gitbook/assets/image (5) (1) (1).png>)

### <mark style="color:orange;">Beacon Frames</mark>

Beacon frames are the most common packets as they are sent at a rate of approximately 10 times per second. Beacons are broadcast by the AP to keep the network synchronized.

![](<../../.gitbook/assets/image (50) (1) (1) (1) (1) (1).png>)

The beacons contain useful information about the network such as the network name (unless SSID broadcast is disabled), the capabilities of the AP, the data rates available, etc. Beacons are typically sent every 102.4ms at a rate of 1 Mbit for 802.11b and 2 Mbit for 802.11a or g. This value can be changed.

### <mark style="color:orange;">Probe Frames</mark>

Wireless probe frames are used to scan for existing access points.

<mark style="color:green;">Probe Request :</mark>&#x20;

robe requests are sent by wireless stations to determine what APs are within range and what their capabilities are

![](<../../.gitbook/assets/image (48) (1) (1) (1).png>)

<mark style="color:green;">Probe Response :</mark>&#x20;

A probe response is only sent if the rate and ESSID values are the same as the ones that are supported by the node. The node that answers the request is the last node that sent out a beacon. A node can be an AP if the network is in infrastructure mode or a station if it is in ad-hoc (IBSS) mode.

![](<../../.gitbook/assets/image (13) (1) (1).png>)

### <mark style="color:orange;">Authentication Frames</mark>

Within an authentication frame, the Authentication Algorithm identifies the type of authentication used. A value of “0” is used to indicate Open System authentication and a value of “1” is used for Shared Key authentication.

![](<../../.gitbook/assets/image (4) (1).png>)

The authentication process consists of several authentication frames (the exact number of frames exchanged can vary). The Authentication Transaction Sequence Number keeps track of the current state of the authentication process and can take values from 1 to 65535. The challenge text will only be present on shared authentication systems.

The Status code value will indicate either success (0) or failure (other than 0) in the authentication process.

### <mark style="color:orange;">Association/Reassociation Frames</mark>

Once a station successfully authenticates to an AP, it needs to perform an association before fully joining the network. An association request frame has the following structure.

![](<../../.gitbook/assets/image (38) (1) (1).png>)

### <mark style="color:orange;">Reassociation Request Frame</mark>

A reassociation request has a structure that is nearly identical to the association request except that it also has a Source Address field.

![](<../../.gitbook/assets/image (50) (1) (1) (1) (1).png>)

### <mark style="color:orange;">Association Response Frame</mark>

Access points respond to an association request with an Association Response either rejecting or accepting the association request. The association response has the following structure.

![](<../../.gitbook/assets/image (34) (1) (1).png>)

### <mark style="color:orange;">Disassociation/Deauthentication Frame</mark>

![](<../../.gitbook/assets/image (21) (1) (1).png>)

The table below outlines different values that can be used for the Reason code in the frame.

![](<../../.gitbook/assets/image (51) (1) (1) (1).png>)

### <mark style="color:orange;">Data Frames</mark>

The purpose of a data frame is to transfer data from an upper layer of a station to another wireless or wired station on the network. there are a number of different types of data frames. The table below will help you in remembering them.

![](<../../.gitbook/assets/image (53) (1) (1) (1).png>)

### <mark style="color:orange;">Null Frame</mark>

ull frames consist only of MAC headers and a FCS. They are used by stations to indicate that they are going into power-saving mode.

## <mark style="color:red;">Wireless Connection and Transmission Process</mark>

![](<../../.gitbook/assets/image (49) (1) (1) (1).png>)

We can separate this process into 3 main parts:

#### <mark style="color:green;">Probe</mark>

1. The STA first sends a probe on all channels to find the AP
2. The APs in range answer the probe request



#### <mark style="color:green;">Authentication</mark>

1. The STA authenticates to the AP, by default to the one with the best signal
2. The authentication process occurs (the length of the process varies)
3. The AP sends a response to the authentication



#### <mark style="color:green;">Association</mark>

1. The STA sends an association request
2. The AP sends an association response
3. The STA can communicate with the network

After this process is completed, data can then be exchanged on the network.

{% hint style="info" %}
For WPA encryption, there is another phase, key exchange and verification, that happens just after association before being able to use the network.
{% endhint %}

## <mark style="color:red;">Security Technology</mark>

### <mark style="color:orange;">WEP</mark>

WEP, or wired equivalent privacy, was the first wireless security scheme employed. As it name implies, it was designed to provide security to the end-user that was essentially equivalent to the privacy that was enjoyed in a wired environment. Unfortunately, it failed miserably.

For a number of reasons, WEP is extraordinarily easy to crack because of a flawed implementation of the RC4 encryption algorithm. It's not unusual to be able to crack WEP in less than 5 minutes. This is because WEP used a very small (24-bit) initialization vector (IV) that could be captured in the datastream, and this IV could then be used to discover the password using statistical techniques.

### <mark style="color:orange;">WPA</mark>

WPA was the response by the industry to the revealed weaknesses of WEP. It's often referred to as WPA1 to distinguish it from WPA2.

WPA used Temporal Key Integrity Protocol (TKIP) to improve the security of WEP without requiring new hardware. It still uses WEP for encryption, but it makes the statistical attacks used to crack WEP much more difficult and time-consuming.

### <mark style="color:orange;">WPA2-PSK</mark>

WPA2-PSK is the implementation of WPA2 for the home or small business user. As the name implies, it's the WPA2 implementation that uses a pre-shared key (PSK). It's this security standard that is used by most households today, and although it's far more secure, it's still vulnerable to various attacks.

{% hint style="info" %}
A feature that was added in 2007 called Wi-Fi Protected Setup, or WPS, allows us to bypass the security in WP2-PSK .
{% endhint %}

### <mark style="color:orange;">WPA2-AES</mark>

WPA2-AES is the enterprise implementation of WPA2. It uses the Advanced Encryption Standard or AES to encrypt data and is the most secure. It's often coupled with a RADIUS server that is dedicated for authentication. Although cracking it is possible, it significantly more difficult.

### <mark style="color:orange;">WPA3</mark>

Similar to WPA2, WPA3 includes WPA3-Personal and WPA3-Enterprise.

<mark style="color:green;">**WPA3-Personal: Enhanced Password Protection**</mark> <mark style="color:green;"></mark><mark style="color:green;"></mark> : WPA3-Personal uses Simultaneous Authentication of Equals (SAE) to replace PSK authentication in WPA2-Personal.

In WPA2, PSK authentication involves a 4-way handshake for key negotiation. Before the negotiation, a PMK is generated based on the service set identifier (SSID) and PSK, which are both fixed. Therefore, the generated PMK is fixed and can be calculated. As a result, the same key is used for each reinstallation. The SAE protocol used by WPA3 adds an SAE handshake before the original PSK 4-way handshake and introduces a dynamic random variable in the PMK generation process. Therefore, the PMK negotiated each time is different, ensuring the randomness of the key. As such, SAE provides a more secure key authentication mechanism for WPA3 to resolve the security risks exposed by WPA2. SAE enables individuals or home users to set Wi-Fi passwords that are easier to remember and provide the same security protection even if the passwords are not complex enough.

<mark style="color:green;">**WPA3-Enterprise: Enhanced Security**</mark> :  Based on WPA2-Enterprise, WPA3-Enterprise provides WPA3-Enterprise 192-bit, a more secure optional mode. This mode provides the following security protection measures:

* Data protection: The 192-bit Suite-B security suite is used. Compared with the 128-bit key used by WPA2, this suite increases the key length to 192 bits, further improving the password defense strength.
* Key protection: The more secure HMAC-SHA-384 algorithm is used to export and confirm keys in the 4-way handshake phase. (HMAC: hash-based message authentication code; SHA: secure hash algorithm)
* Traffic protection: The more secure Galois-Counter Mode Protocol-256 (GCMP-256) is used to protect wireless traffic after STAs go online.
* Protected management frame (PMF): The Galois Message Authentication Code-256 (GMAC-256) of GCMP is used to protect multicast management frames.

<mark style="color:green;">**OWE Authentication: Open Network Protection**</mark>** :** Most Wi-Fi networks in public venues, such as airports, stations, and cafes, use the traditional open authentication mode. With OWE, users can access Wi-Fi networks without entering passwords, and data transmitted between users and Wi-Fi networks is not encrypted. This increases the risk of unauthorized network access.

WPA3 introduces an Enhanced Open network authentication mode for open networks. This allows users to access the network without entering the password, facilitating user access to the open Wi-Fi networks. Additionally, OWE uses the Diffie-Hellman key exchange algorithm to exchange keys between users and Wi-Fi devices, encrypting transmitted data and protecting user data security.
