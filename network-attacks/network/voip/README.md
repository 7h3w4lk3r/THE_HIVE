# VOIP

## :information\_source: Introduction

[Voice Over IP](https://datatracker.ietf.org/doc/html/rfc6405)

{% embed url="https://www.hackingarticles.in/penetration-testing-lab-setup-voip" %}

#### Allows making audio calls in a network (or  over the internet). default port is 5060 for SIP signaling

![](<../../../.gitbook/assets/image (278).png>)

### VOIP Protocols

The most common VoIP protocols are listed as follows:

| VoIP Protocol                                                                                   | Summary                                                                                                                                                                                                                                   |
| ----------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Authenticated Identity Body (AIB)                                                               | This is a method of sharing authenticated identity with parties in the network and uses a special MIME format. Works in conjunction with SIP S/MIME. Discussed in RFC 3893                                                                |
| Call Admission Control (CAC)                                                                    | A type of quality of service and congestion control for voice, CAC ensures that there is no oversubscription of voice calls on the VoIP network. It prevents any potential for excessive voice calls and protects existing voice traffic. |
| Distributed Universal Number Discovery                                                          | Somewhat self explanatory protocol that enables directory information to be collected.                                                                                                                                                    |
| H.248 (Gateway Control Protocol)                                                                | Also known as Megaco Protocol. This is a master/slave protocol and allows a media gateway controller to control media gateways. Uses H.323 and SIP to communicate between controllers. Discussed in detail in RFC 3525.                   |
| H.323                                                                                           | Commonly used by telecom equipment manufacturers and service providers. ITU recommendation that addresses call signaling and control. H.323 is a master/slave protocol.                                                                   |
| Inter-Asterisk Exchange (IAX)                                                                   | Asterisk protocol but also used by many other equipment manufacturers as a communications protocol between servers. IAX has now been replaced by IAX2. Discussed in detail in RFC 5456.                                                   |
| Media Gateway Control Protocol  (MGCP)                                                          | VoIP signaling and call control protocol that enables communications with the PSTN (public switched telephone network). Master/Slave protocol. The core RFC for MGP is RFC 3435 and there are also a number of related RFCs.              |
| Network Voice Protocol                                                                          | Early version of Voice over Internet Protocol.                                                                                                                                                                                            |
| Open Settlement Protocol                                                                        | This is a client server protocol that is used by ISP's for the exchange of authorization and accounting information related to VoIP.                                                                                                      |
| Real-time Transport Protocol (RTP)                                                              | Protocol that manages real-time communications over telephony networks. Discussed in detail in RFC 3550.                                                                                                                                  |
| Registration, Admission and Status (RAS)                                                        | Manages communications between H.323 endpoints and gatekeepers.                                                                                                                                                                           |
| [SIP](https://www.whichvoip.com/articles/sip-technical-guide.htm) (Session Initiation Protocol) | Signaling protocol for voice and video communications. Peer to peer protocol.                                                                                                                                                             |
| SIP Requests                                                                                    | Special commands that are sent as requests by the SIP protocol to peers.                                                                                                                                                                  |
| [SIP Response Codes](https://www.whichvoip.com/articles/sip-technical-guide.htm)                | Codes that are used by the SIP protocol to respond to SIP requests                                                                                                                                                                        |
| Simple Gateway Control Protocol                                                                 | Predecessor to MGCP protocol.                                                                                                                                                                                                             |
| Skinny Call Control Protocol                                                                    | Cisco proprietary protocol used to communicate between Cisco endpoints (IP phones) and the Cisco call-manager phone system.                                                                                                               |
| Skype Protocol                                                                                  | Peer to peer protocol used by Skype.                                                                                                                                                                                                      |
| T.37                                                                                            | Enables the sending of fax messages via email by encapsulating the fax in an email message. Can also work with a fax gateway to operate with a regular fax machine. Commonly referred to as Internet Fax or Online Fax.                   |
| T.38                                                                                            | Enables faxes to be transmitted over a packet network.                                                                                                                                                                                    |

VoIP telephony uses mainly two protocols in order to set up a call and to transport Audio/Video signal. They’re described in the following subsections.



## :ballot\_box\_with\_check: Checklist

*





























&#x20;







