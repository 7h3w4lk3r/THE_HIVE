# SMTP

## Introduction

[Simple Mail Transfer Protocol](https://www.educba.com/smtp-protocol/)&#x20;

used for submission (send) or relay. mostly widely-used SMTP  on linux is **sendmail **service, on windows, its **Microsoft Exchange**. both can be setup to support POP3.

#### submission:

For Explicit SSL/TLS uses port TCP 587 or 2525

For Implicit SSL/TLS uses port TCP 465

#### Relay:

port TCP 25

## Checklist

* [ ] Service Enumeration
* [ ] User enumeration
* [ ] Login brute force







## Enumeration

```
nmap -p smtp* [target]
nc -nv [target ip] 25
use auxiliary/scanner/smtp/smtp_version
nmap -p25 --script smtp-commands 10.10.10.10
lf -connect smtp.mailgun.org:465 #SSL/TLS without starttls command
openssl s_client -starttls smtp -crlf -connect smtp.mailgun.org:587
```

## Connect

```
telnet 192.168.13.26 25 # user IP or domain name
nc 192.168.13.26 25 
```

## User Enumeration

There are 3 methods (SMTP commands) to test for existance of a user in SMTP service:

1. VRFY
2. RCPT
3. EXPN

```
smtp-user-enum: smtp-user-enum -M <MODE> -u <USER> -t <IP>
use auxiliary/scanner/smtp/smtp_enum
nmap -sV --script smtp-enum-users
# use smtp-enum-users.methods={EXPN,RCPT,VRFY}  to test different methods
nmap --script smtp-enum-users.nse [--script-args smtp-enum-users.methods={EXPN,...},...] -p 25,465,587 <host>
```

### Telnet

#### RCPT TP

```
telnet 10.0.10.1 25
HELO x
MAIL FROM:test@test.org
RCPT TO:test

# last command will return "User Unknown" if the user doesnt exist
# it will return "Recipient ok" if the user does exist
```

#### VRFY

```
VRFY root
# responds with response code 250 if root user exists
# responds with response code 550 if root doesnt exist
```

#### EXPN

```
EXPN root
250 2.1.5 <ed.williams@myhost>  # root doesnt exist
EXPN sshd
2.1.5 sshd privsep <sshd@mail2>  # sshd exists
```

### python script for verifying users

{% hint style="info" %}
change the "method" variable to test for different methods of user enumeration.
{% endhint %}

```python
#!/usr/bin/python
import socket
import sys
method = "VRFY "
if len(sys.argv) != 2:
print "Usage: vrfy.py [username]"
sys.exit(0)
# Create a Socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
# Connect to the Server
connect = s.connect(('10.11.1.217',25))
# Receive the banner
banner = s.recv(1024)
print banner
# VRFY a user
s.send( method + sys.argv[1] + '\r\n')
result = s.recv(1024)
print result
# Close the socket
s.close() 
```

{% embed url="https://github.com/cytopia/smtp-user-enum" %}

{% embed url="https://github.com/z0mbiehunt3r/smtp-enum" %}

## Login Brute Force

```
nmap -p 25 --script smtp-brute <host>

hydra -l username -P /usr/share/wordlists/fasttrack.txt 192.168.56.101 pop3 -s 55007 -vv
```

## NTLM Auth - Information disclosure







## Manual test

### Basic SMTP Commands

#### HELO

&#x20;(Hello) The client sends this command to the SMTP server to identify itself and initiate the SMTP conversation. The domain name or IP address of the SMTP client is usually sent as an argument together with the command (e.g. “HELO client.example.com”). If a domain name is used as an argument with the HELO command, it must be a fully qualified domain name (also called FQDN)

```
helo w4lk3r
```

#### MAIL FROM&#x20;

Specifies the e-mail address of the sender. This command also tells the SMTP server that a new mail transaction is starting and makes the server to reset all its state tables and buffers etc. This command is usually sent as the first command after the identifying and login process. If the senders e-mail address is accepted the server will reply with a 250 OK reply code. Example:

```
C: MAIL FROM:[mail@samlogic.com]
S: 250 OK
```

### RCPT TO&#x20;

(Recipient To) Specifies the e-mail address of the recipient. This command can be repeated multiple times for a give n e-mail message in order to deliver a single e-mail message to ultiple recipients. The example below shows how this command can be used to send same e-mail message to two recipients:

```
C: MAIL FROM:[mail@samlogic.com]
S: 250 OK
C: RCPT TO:[john@mail.com]
S: 250 OK
C: RCPT TO:[peggy@mail.com]
S: 250 OK
```

#### DATA&#x20;

The DATA command starts the transfer of th e message contents (body text, attachments etc). After that the DATA command has been sent to the server from the client, the server will respond with a 354 reply code. After that, the message contents can be transferred to the server. When al l message contents have been sent, a single dot (“.”) must be sent in a li ne by itself. If the message is accepted for delivery, the SMTP server wil l response with a 250 reply code. Example (the message contents is set to italic in the example below):

```
C: DATA
S: 354 Send message content; end with [CRLF].[CRLF]
C: Date: Thu, 21 May 2008 05:33:29 -0700
C: From: SamLogic [mail@samlogic.com]
C: Subject: The Next Meeting
C: To: john@mail.com
C:
C: Hi John,
C: The next meeting will be on Friday.
C: /Anna.
C: .
S: 250 OK
```

#### RSET&#x20;

(Reset) If the RSET command is sent to the e-mail se rver the current mail transaction will be aborted. The connection wi ll not be closed (this is reserved for the QUIT command, see below) but a ll information about the sender, recipients and e-mail data will be remo ved and buffers and state tables will be cleared.

#### VRFY

&#x20;(Verify) This command asks the server to confirm that a specified user name or mailbo x is valid (exists). If the user name is asked, the full name of the u ser and the fully specified mailbox are returned. In some e-mail servers the VRFY command is ignored because it can be a security hole. The command c an be used to probe for login names on servers. Servers that ignore the VRF Y command will usually send some kind of reply, but they will not send the i nformation that the client asked for

#### NOOP&#x20;

(No operation) The NOOP command does nothing else than makes the receiver to send a n OK reply. The main purpose is to check that the server is still connec ted and is able to communicate with the client.

#### QUIT&#x20;

Asks the server to close the connection. If the connection can be closed the servers replies with a 221 numerical code and then is the session closed.
