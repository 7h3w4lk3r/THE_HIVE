---
description: (TCP 110, 995)
---

# 🟨 POP3

## :information\_source: <mark style="color:blue;">Introduction</mark>

#### [Post Office Protocol 3](https://tools.ietf.org/html/rfc1939)

Most used protocol for receiving Emails. a one-way client-server protocol which can download the mails from server and hold them on client side.

TCP port 110 for non-encrypted mail retrieval

TCP port 995 for using SSL/TLS.

## :ballot\_box\_with\_check: <mark style="color:blue;">Checklist</mark>

* [ ] Login brute force
* [ ] Check for POP - NTLM auth
* [ ] Try credential capture
* [ ] use POP3 commands manually
* [ ] Check for CVEs

## <mark style="color:red;">Enumeration</mark>

```
nmap -sV --script pop3-capabilities
nc -nv <IP> 110
openssl s_client -connect <IP>:995 -crlf -quiet
use auxiliary/scanner/pop3/pop3_version
```

## <mark style="color:red;">Login Brute Force</mark>

```
nmap -p110 --script pop3-brute <target>
use auxiliary/scanner/pop3/pop3_login
hydra -l USERNAME -P /path/to/passwords.txt -f <IP> pop3 -V
hydra -S -v -l USERNAME -P /path/to/passwords.txt -s 995 -f <IP> pop3 -V
```

## <mark style="color:red;">POP-NTLM Auth</mark>

```
nmap -sV --script pop3-ntlm-info <target>
```

## <mark style="color:red;">Capture Authention Credentials</mark>

```
use auxiliary/server/capture/pop3
```

### <mark style="color:orange;">POP3 commands</mark>

```
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
