# IMAP

## Introduction

[Internet Message Access Protocol](https://whatis.techtarget.com/definition/IMAP-Internet-Message-Access-Protocol)

Like POP, IMAP is used for email retrieval (client side protocol).

port TCP 143 for no encryption

port TCP 993 for SSL/TLS

## &#x20;Checklist

* [ ] Login brute force
* [ ] Check for IMAP - NTLM auth
* [ ] Use curl to navigate
* [ ] Try to capture IMAP credentials&#x20;

## Enumeration

```
nc -nv <IP> 143
use auxiliary/scanner/imap/imap_version
openssl s_client -connect <IP>:993 -quiet
nmap -sV --script imap-apabilities
```

## Login Brute Force

```
nmap --script imap-brute
hydra -l USERNAME -P /path/to/passwords.txt -f <IP> imap -V
hydra -S -v -l USERNAME -P /path/to/passwords.txt -s 993 -f <IP> imap -V
nmap -sV --script imap-brute -p <PORT> <IP>
```

## Capture IMAP Authentication

```
use auxiliary/server/capture/imap
```

## IMAP - NTLM Auth

If the server supports NTLM auth (Windows) you can obtain sensitive info (versions):

### Manual

```
telnet example.com 143
* OK The Microsoft Exchange IMAP4 service is ready.
>> a1 AUTHENTICATE NTLM
+
>> TlRMTVNTUAABAAAAB4IIAAAAAAAAAAAAAAAAAAAAAAA= 
+ TlRMTVNTUAACAAAACgAKADgAAAAFgooCBqqVKFrKPCMAAAAAAAAAAEgASABCAAAABgOAJQAAAA9JAEkAUwAwADEAAgAKAEkASQBTADAAMQABAAoASQBJAFMAMAAxAAQACgBJAEkAUwAwADEAAwAKAEkASQBTADAAMQAHAAgAHwMI0VPy1QEAAAAA
```

### Automated

```
nmap --script imap-ntlm-info [target ip]
```

## Navigation with CURL

Listing mailboxes (imap command `LIST "" "*"`)

```
$ curl -k 'imaps://1.2.3.4/' --user user:pass
```

Listing messages in a mailbox (imap command `SELECT INBOX` and then `SEARCH ALL`)

```
$ curl -k 'imaps://1.2.3.4/INBOX?ALL' --user user:pass
```

The result of this search is a list of message indicies.

Its also possible to provide more complex search terms. e.g. searching for drafts with password in mail body:

```
$ curl -k 'imaps://1.2.3.4/Drafts?TEXT password' --user user:pass
```

Downloading a message (imap command `SELECT Drafts` and then `FETCH 1 BODY[]`)

```
$ curl -k 'imaps://1.2.3.4/Drafts;MAILINDEX=1' --user user:pass
```

