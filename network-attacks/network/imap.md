---
description: (TCP 143, 993)
---

# ⭕ IMAP

## :information\_source: <mark style="color:blue;">Introduction</mark>

#### [Internet Message Access Protocol](https://datatracker.ietf.org/doc/html/rfc3501)

Like POP, IMAP is used for email retrieval (client side protocol).

port TCP 143 for no encryption

port TCP 993 for SSL/TLS

## :ballot\_box\_with\_check: <mark style="color:blue;">Checklist</mark>

* [ ] Login brute force
* [ ] Check for IMAP - NTLM auth
* [ ] Use curl to navigate
* [ ] Try to capture IMAP credentials

## <mark style="color:red;">Enumeration</mark>

```
nc -nv <IP> 143
use auxiliary/scanner/imap/imap_version
openssl s_client -connect <IP>:993 -quiet
nmap -sV --script imap-apabilities
```

## <mark style="color:red;">Login Brute Force</mark>

```
nmap --script imap-brute
hydra -l USERNAME -P /path/to/passwords.txt -f <IP> imap -V
hydra -S -v -l USERNAME -P /path/to/passwords.txt -s 993 -f <IP> imap -V
nmap -sV --script imap-brute -p <PORT> <IP>
```

## <mark style="color:red;">Capture IMAP Authentication</mark>

```
use auxiliary/server/capture/imap
```

## <mark style="color:red;">IMAP - NTLM Auth</mark>

If the server supports NTLM auth (Windows) you can obtain sensitive info (versions):

### <mark style="color:orange;">Manual</mark>

```
telnet example.com 143
* OK The Microsoft Exchange IMAP4 service is ready.
>> a1 AUTHENTICATE NTLM
+
>> TlRMTVNTUAABAAAAB4IIAAAAAAAAAAAAAAAAAAAAAAA= 
+ TlRMTVNTUAACAAAACgAKADgAAAAFgooCBqqVKFrKPCMAAAAAAAAAAEgASABCAAAABgOAJQAAAA9JAEkAUwAwADEAAgAKAEkASQBTADAAMQABAAoASQBJAFMAMAAxAAQACgBJAEkAUwAwADEAAwAKAEkASQBTADAAMQAHAAgAHwMI0VPy1QEAAAAA
```

### <mark style="color:orange;">Automated</mark>

```
nmap --script imap-ntlm-info [target ip]
```

## <mark style="color:red;">Navigation with CURL</mark>

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
