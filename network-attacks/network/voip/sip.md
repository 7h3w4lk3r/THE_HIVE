# SIP

## Session Initiation Protocol (SIP)

The Session Initiation Protocol (SIP) allows us to establish communication, end or change voice or video calls. The voice or video traffic is transmitted via the Real-Time Protocol (RTP) protocol. SIP is an application layer protocol that uses UDP or TCP for traffic. By default, SIP uses port 5060 UDP/TCP for unencrypted traffic or port 5061 for TLS encrypted traffic. As we will see later, Man-in-the-Middle (MITM) attack vectors exist for all types of communication, including VoIP/SIP. Therefore, encryption is a necessary compensating control to have in place regardless of the environment or service method Session Initiation Protocol is ASCII based and very similar to the HTTP protocol as it uses a Request/Response Model. Requests to the SIP client are made through SIP URI and AGI via a user-agent similar to an HTTP request made by a web browser.

![](<../../../.gitbook/assets/image (289) (2).png>)

### **SIP Requests**

The following request types are common within SIP:

| **Sno.** | **Request** | **Description**                                                             |
| -------- | ----------- | --------------------------------------------------------------------------- |
| 1.       | INVITE      | The client is being invited to participate in a call session                |
| 2.       | ACK         | Confirms that the client has received a final response to an INVITE request |
| 3.       | BYE         | Terminates a call and can be sent by either the caller or the caller        |
| 4.       | CANCEL      | Deletes any pending request                                                 |
| 5.       | OPTIONS     | Queries the capabilities of servers                                         |
| 6.       | REGISTER    | Registers the address listed in the header field with a SIP server          |
| 7.       | PRACK       | Provisional Acknowledgement                                                 |
| 8.       | SUBSCRIBE   | Subscribes for an Event of Notification from the Notifier                   |
| 9.       | NOTIFY      | Notify the subscriber of a new Event                                        |
| 10.      | PUBLISH     | Publishes an event to the Server                                            |
| 11.      | INFO        | Sends mid-session information that does not modify the session state        |
| 12.      | REFER       | Asks recipient to issue SIP request (Call Transfer)                         |
| 13.      | MESSAGE     | Transports instant messages using SIP                                       |

### **SIP Responses**

We can understand the Responses using the Response code. The general categories of the Response codes are given below:

* 1xx (Informational)
* 2xx (Success)
* 3xx (Redirection)
* 4xx (Failed requests)
* 5xx (Web server cannot complete request)
* 6xx (Global errors)

### **SIP Interaction Structure**

The Typical SIP Interaction Structure consists of the following:

1. The sender initiates an INVITE request.
2. The receiver sends back a 100 (Trying) response.
3. The sender starts ringing by sending a 180 (Ringing) response.
4. The receiver picks up the phone and a 200 success response is sent (OK).
5. ACK is sent by the initiator.
6. The call started using RTP.
7. BYE request sent to end the call.
