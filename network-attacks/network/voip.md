# VOIP

## :information\_source: Introduction

[Voice Over IP](https://datatracker.ietf.org/doc/html/rfc6405)

#### Allows making audio calls in a network (or  over the internet). default port is 5060 for SIP signaling

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

### Real-Time Protocol (RTP)

The Real-time Transport Protocol (RTP) is a standardized packet format used by IP networks in order to deliver audio/video signal. It was designed for end-to-end, real-time, transfer of stream data and it’s regarded as the primary standard for audio/video transport in IP networks and it is used with an associated profile and payload format.

![](<../../.gitbook/assets/image (278).png>)

RTP is used in conjunction with the Real-Time Control Protocol (RTCP) which is used to monitor transmission statistics and Quality of Service aiding synchronization of multiple streams. While RTP is originated and received on even port numbers, the associated RTCP packets use the next higher odd port number.

The protocol provides facilities for jitter compensation (jittering is rather common on a Packet-Switched Network since communication is provided by network Routers), detection of out of sequence arrival in data and allows data transfer to multiple destinations through IP multicast.

Real-time applications require timely delivery of information and can tolerate some packet loss usually than an excessive delay. Thus, in order to achieve this goal the Transmission Control Protocol (TCP) is normally not used by RTP since TCP favors reliability over timeliness, RTP systems are instead usually built on the User Datagram Protocol (UDP).

The audio sampling rate is typically either 8000Hz or 16000Hz and the rate that RTP packets are transmitted is determined by the audio Codec by mean of its Packetization Period. Whether those packets actually arrive at a fixed rate at the receiving endpoint depends on the network performance. RTP packets might be lost by Routers, might arrive at the receiving endpoint out of sequence, or could be even duplicated when they transit through the network.

Hence receiving endpoints are designed with the assumption that RTP packets will not arrive at the precise rate they were transmitted. About this reasons an endpoint incorporate a Jitter Buffer having parameters in order to manipulate the characteristics of time buffering in an attempt to produce the highest Quality of Service during the playback. Jitter Buffer uses RTP header information to accomplish its functions.

### Session Initiation Protocol (SIP)

&#x20;SIP is a telephone signaling protocol used by VoIP in order to initiating, managing and terminating voice sessions in Packet Switched Networks. SIP sessions involve one or more participants and can use either unicast or multicast communication. SIP is text-encoded and highly extensible since it may be extended to accommodate features and services such as call control services, mobility and interoperability with existing telephony systems.

That are 4 types of logical SIP entities, each one participates in SIP communication as a client (the entity which initiates the Requests), as a server (the entity which Responds to Requests), or as both. One network device can have the functionality of more than one logical SIP entity. In the following the 4 types of logical SIP entities are reported:

* **USER AGENT (UA):** initiate and terminate sessions by exchanging Requests and Responses. UA is an application, which contains both a User Agent Client (UAC) and User Agent Server (UAS). UAC is a client application that initiates SIP requests while UAS is a server application that contacts the user when a SIP request is received and that returns a response on behalf of the user. Devices with UA functions are: workstations, IP-phones, Media Gateways, call agents and automated answering services;
* **PROXY SERVER:** intermediary entity that acts as both a server and a client with the purpose of making Requests on behalf of other clients. Requests are serviced either internally or by passing them on (possibly after translation) to other servers. A Proxy interprets and, when it’s necessary, rewrites a Request message before forwarding it;
* **REDIRECT SERVER:** server that accepts a SIP Request, maps the SIP address of the called party into zero (if there isn’t known address) or more new addresses and returns them to the client. It does not not pass the Request on to other servers;
* **REGISTRAR:** accepts REGISTER Requests in order to updating a location database with the contact information of the user specified in the Request.

#### There are two types of SIP messages:

* Request Messages: they’re sent from the client to the server;
* Response Messages: they’re sent from the server to the client.

#### In the following Request Messages types are reported:

* INVITE: initiates a call and it can changes call parameters, in this case it’s called re-INVITE;
* ACK: confirms a final response for the INVITE message;
* BYE: used in order to terminate a call;
* CANCEL: cancels searches and ringing;
* OPTIONS: queries the capabilities of the other side;
* REGISTER: used to register with the Location Service;
* INFO: sends mid-session information that does not modify the session state.

#### Response Messages contain numeric codes, there are 2 types of responses and 6 types. In the following the Response types are reported:

1. Provisional: its own class is1xx, this kind of responses are used by the server to indicate a progress state but they can’t terminate SIP transactions;
2. Final: its own classes are 2xx, 3xx, 4xx, 5xx, 6xx, this kind of responses terminate the SIP transactions.

#### The different types of classes, divided by their prefix number, are reported in the following:

* 1xx: provisional, searching, ringing and queuing. Two examples of these messages are ‘100 Continue’ and ‘180 Ringing’;
* 2xx: success. An example is the message is ‘200 OK’;
* 3xx: redirection and forwarding. Examples are messages ‘301 Moved Permanently’ and ‘302 Moved Temporarly’;
* 4xx: request failure for client mistakes. The messages ‘400 Bad Request’ and ‘408 Request Time-Out’ are two examples of these messages;
* 5xx: server failures.
* 6xx: global failure such as busy, refusal, not available. The messages ‘600 Busy’ and ‘604 Does Not Exist’ are two examples.

SIP messages are composed of 3 parts:

* **Start Line:** each SIP message begins with this part. The Start Line conveys the message type (method type in Requests and Response code in responses) and the protocol version. The Start Line may be either Request-line (request message that includes a Request URI, which indicates the user or service to which this request is being addressed. Unlike the “To” field) or Status-line (response message which holds the numeric Status-code and its associated textual phrase);

![](<../../.gitbook/assets/image (296).png>)

* **Header Fields:** used to convey message attributes and to modify message meaning. Like an HTTP request from a browser is made using an URL, SIP uses an e-mail like addresses which typical format is: user/phone@domain/ip. They can span multiple lines. Some SIP headers such as Via, Contact, Route and Request-Route can appear multiple times in a message or, alternatively, can take multiple comma-separated values in a single header occurrence;
* **Body:** this is the content of the message and is used to describe the session to be initiated, this may include audio and video codec types that, sampling rates, etc.; It alternatively may be used to contain opaque textual or binary data of any type which relates in some way to the session. Message bodies can appear both in Request and in Response Messages. Possible body types include: Session Description Protocol (SDP) and Multipurpose Internet Mail Extensions (MIME).

Figure 2 shows a trivial SIP session, registered by mean of a Network Analyzer called Wireshark, that reports an interaction between a UAC and a UAS which is established and then terminated. UAC has IP address 192.168.101.190 and UAS has 192.168.101.105. In particular packet 421 is an INVITE Request Message sent to the user 1000. Then, the Response Message packets 423 and 424 belonging to class 1xxx, said respectively a call continuation and the ring back tone. After about 10 seconds the called user answer is stated by packet 647 which reports a Response Message OK belonging to the class 2xxx, now the telephone call is established. The telephone call duration is about 40 second, then the caller hang up the telephone, it is stated by packet 4985 which reports a BYE Request Message in order to close the call.

Figure 3 reports a detail of the packet number 421 which is registered again by mean of Wireshark. It’s an INVITE Request Message where Start Line, Header Fields and Body are clearly visible





&#x20;







