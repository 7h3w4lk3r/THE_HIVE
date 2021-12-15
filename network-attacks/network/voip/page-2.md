# Page 2

### Real-Time Protocol (RTP)

The RTP is a network protocol for delivering audio and video over networks. RTP protocol is used in communication and entertainment systems that involve streaming media such as telephony and video or teleconference applications. RTP default port from 16384 to 32767, those ports used for sip calls. In our scenario, we are using the UDP port range 10000-20000 for RTP-the media stream, voice, and video channels.

![](<../../../.gitbook/assets/image (295).png>)

RTP is used in conjunction with the Real-Time Control Protocol (RTCP) which is used to monitor transmission statistics and Quality of Service aiding synchronization of multiple streams. While RTP is originated and received on even port numbers, the associated RTCP packets use the next higher odd port number.

The protocol provides facilities for jitter compensation (jittering is rather common on a Packet-Switched Network since communication is provided by network Routers), detection of out of sequence arrival in data and allows data transfer to multiple destinations through IP multicast.

Real-time applications require timely delivery of information and can tolerate some packet loss usually than an excessive delay. Thus, in order to achieve this goal the Transmission Control Protocol (TCP) is normally not used by RTP since TCP favors reliability over timeliness, RTP systems are instead usually built on the User Datagram Protocol (UDP).

The audio sampling rate is typically either 8000Hz or 16000Hz and the rate that RTP packets are transmitted is determined by the audio Codec by mean of its Packetization Period. Whether those packets actually arrive at a fixed rate at the receiving endpoint depends on the network performance. RTP packets might be lost by Routers, might arrive at the receiving endpoint out of sequence, or could be even duplicated when they transit through the network.

Hence receiving endpoints are designed with the assumption that RTP packets will not arrive at the precise rate they were transmitted. About this reasons an endpoint incorporate a Jitter Buffer having parameters in order to manipulate the characteristics of time buffering in an attempt to produce the highest Quality of Service during the playback. Jitter Buffer uses RTP header information to accomplish its functions.

