# SMTP/POP3

The following information does apply for both Windows and Linux-based mail servers since “SMTP” is the underlying protocol, but since a large majority of mail servers in-use are Linux-based, we’ll be focusing on enumerating users from “Sendmail,” a popular Open-Source \*NIX-based mail server. In the wild, you will mostly encounter Sendmail, Postfix, Exim or Microsoft Exchange. The following techniques can apply to all. The first task is to enumerate which options, “verbs” or “features” are enabled on an SMTP server, usually found on TCP/25; this can be accomplished with the “smtp-commands” Nmap NSE script:

### nmap scan

```text
nmap --script smtp-commands 192.168.13.26 -p 25
nmap -p smtp [target]
nmap -p smtp* [target]
nmap -sV --script smtp-open-relay -v [target]
nmap -sV --script smtp-open-relay --script-args smtp-open-relay.ip=[ip] [target]
nmap -sV --script smtp-open-relay --script-args smtp-open-relay.domain=[domain] [target]
nmap -sV --script smtp-open-relay -v --script-args smtp-open-relay.to=[Destination e-mail address],smtp-open-relay.from=[Source e-mail address] [target]
nmap -p25 --script smtp-brute [target]
nmap -sV --script smtp-strangeport [target]
nmap -p25 --script smtp-enum-users [target]
nmap -p25 --script smtp-enum-users --script-args smtp-enum-users.methods={VRFY,EXPN,RCPT} [target]
nmap -p25 --script smtp-enum-users --script-args smtp-enum-users.domain=[domain] [target]
nmap -p25,465,587 --script smtp-ntlm-info --script-args smtp-ntlm-info.domain=[target domain] [target]
```

We can also achieve the same results with a direct connection to port 25 using telnet or netcat by simply issuing a “help” followed by \[enter\] command once connected.

```text
telnet 192.168.13.26 25
nc 192.168.13.26 25 
```

### SMTP\_USER\_ENUM

```text
smtp-user-enum -t 192.168.56.103 -M RCPT -u root
```

### metasploit modules

```text
use auxiliary/scanner/smtp/smtp_enum
```

### python script for verifying users

```python
#!/usr/bin/python
import socket
import sys
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
s.send('VRFY ' + sys.argv[1] + '\r\n')
result = s.recv(1024)
print result
# Close the socket
s.close() 
```

### bruteforce

```text
hydra -l username -P /usr/share/wordlists/fasttrack.txt 192.168.56.101 pop3 -s 55007 -vv
```

## Manual test

### Basic POP3 commands

```text
USER Your user name for this mail server
PASS Your password.
QUIT End your session.
STAT Number and total size of all messages
LIST Message# and size of message
RETR message# Retrieve selected message
DELE message# Delete selected message
NOOP No-op. Keeps you connection open.
RSET Reset the mailbox. Undelete deleted messages.
```

### Basic SMTP Commands

#### HELO

 \(Hello\) The client sends this command to the SMTP server to identify itself and initiate the SMTP conversation. The domain name or IP address of the SMTP client is usually sent as an argument together with the command \(e.g. “HELO client.example.com”\). If a domain name is used as an argument with the HELO command, it must be a fully qualified domain name \(also called FQDN\)

```text
helo w4lk3r
```

#### MAIL FROM 

Specifies the e-mail address of the sender. This command also tells the SMTP server that a new mail transaction is starting and makes the server to reset all its state tables and buffers etc. This command is usually sent as the first command after the identifying and login process. If the senders e-mail address is accepted the server will reply with a 250 OK reply code. Example:

```text
C: MAIL FROM:[mail@samlogic.com]
S: 250 OK
```

### RCPT TO 

\(Recipient To\) Specifies the e-mail address of the recipient. This command can be repeated multiple times for a give n e-mail message in order to deliver a single e-mail message to ultiple recipients. The example below shows how this command can be used to send same e-mail message to two recipients:

```text
C: MAIL FROM:[mail@samlogic.com]
S: 250 OK
C: RCPT TO:[john@mail.com]
S: 250 OK
C: RCPT TO:[peggy@mail.com]
S: 250 OK
```

#### DATA 

The DATA command starts the transfer of th e message contents \(body text, attachments etc\). After that the DATA command has been sent to the server from the client, the server will respond with a 354 reply code. After that, the message contents can be transferred to the server. When al l message contents have been sent, a single dot \(“.”\) must be sent in a li ne by itself. If the message is accepted for delivery, the SMTP server wil l response with a 250 reply code. Example \(the message contents is set to italic in the example below\):

```text
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

#### RSET 

\(Reset\) If the RSET command is sent to the e-mail se rver the current mail transaction will be aborted. The connection wi ll not be closed \(this is reserved for the QUIT command, see below\) but a ll information about the sender, recipients and e-mail data will be remo ved and buffers and state tables will be cleared.

#### VRFY

 \(Verify\) This command asks the server to confirm that a specified user name or mailbo x is valid \(exists\). If the user name is asked, the full name of the u ser and the fully specified mailbox are returned. In some e-mail servers the VRFY command is ignored because it can be a security hole. The command c an be used to probe for login names on servers. Servers that ignore the VRF Y command will usually send some kind of reply, but they will not send the i nformation that the client asked for

#### NOOP 

\(No operation\) The NOOP command does nothing else than makes the receiver to send a n OK reply. The main purpose is to check that the server is still connec ted and is able to communicate with the client.

#### QUIT 

Asks the server to close the connection. If the connection can be closed the servers replies with a 221 numerical code and then is the session closed.

