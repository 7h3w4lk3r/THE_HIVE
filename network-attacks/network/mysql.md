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
