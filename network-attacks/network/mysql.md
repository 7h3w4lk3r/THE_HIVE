# MySQL

### Basics <a href="basics" id="basics"></a>

```bash
# Try connection from outside
mysql --host <IP> -u root -proot
        
# Connection from the target machine
mysql -u root -p root database
```

### Classical commands <a href="classical-commands" id="classical-commands"></a>

```bash
show databases;
use database_name;
show tables;
describe table_name;
select host, user, password from mysql.user;

```

### Identification and Scan <a href="identification-and-scan" id="identification-and-scan"></a>

```bash
# Using nmap NSE scripts
nmap -n -sV --version-intensity=5 -Pn -p T:3306 --script=xxxx <IP>

# Audits MySQL database server security configuration
mysql-audit

# Bruteforce accounts and password against a MySQL Server
mysql-brute

# Attempts to list all databases on a MySQL server. (creds required)
mysql-databases

#Dumps the password hashes from an MySQL server in a format suitable (creds required)
mysql-dump-hashes

# Checks for MySQL servers with an empty password for root or anonymous.
mysql-empty-password

# Performs valid-user enumeration against MySQL server using a bug
mysql-enum

# Connects to a MySQL server and prints information such as the protocol and version numbers, thread ID, status, capabilities, and the password salt.
mysql-info

# Runs a query against a MySQL database and returns the results as a table. (creds required)
mysql-query

# Attempts to list all users on a MySQL server.
mysql-users

# Attempts to show all variables on a MySQL server.
mysql-variables

# Attempts to bypass authentication in MySQL and MariaDB servers by exploiting CVE2012-2122. If its vulnerable, it will also attempt to dump the MySQL usernames and password hashes. 
mysql-vuln-cve2012-2122
```

## MySQL Arbitrary File Read

Load data infile is a very special syntax. Friends who know about injection or often play CTF may be familiar with this syntax. In CTF, we often encounter situations where there is no way to load\_file to read the file. At this time, load data infile is the only possible way to read files. Generally our statement is this:

```
load data infile "/etc/passwd" into table test FIELDS TERMINATED BY '\n';
```

The mysql server will read the server’s / etc / passwd and insert the data into the table according to `'\n'`. But now this statement also requires you to have FILE permissions, and non-local loaded statements are also restricted by `secure_file_priv`.

```
mysql> load data infile "/etc/passwd" into table test FIELDS TERMINATED BY '\n';

ERROR 1290 (HY000): The MySQL server is running with the --secure-file-priv option so it cannot execute this statement
```

If we add a keyword local

```
mysql> load data local infile "/etc/passwd" into table test FIELDS TERMINATED BY '\n';

Query OK, 11 rows affected, 11 warnings (0.01 sec)
Records: 11  Deleted: 0  Skipped: 0  Warnings: 11
```

The client’s file will be read and send to the server. The execution result of the above statement is as follows.

![](../../.gitbook/assets/0-s6pVc0L8-WEq0HGr.png)

#### the PoC can be found here:

{% embed url="https://github.com/allyshka/Rogue-MySql-Server" %}

an overview of the attack

{% embed url="http://russiansecurity.expert/2016/04/20/mysql-connect-file-read" %}

