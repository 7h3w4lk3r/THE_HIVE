# MSSQL

## Enumeration

```
nmap -n -sV --version-intensity=5 -sT -Pn -p T:1433 --script=xxxx <IP>
auxiliary/scanner/mssql/mssql_ping
```

## Login Brute Force

```
nmap --script ms-sql-brute [target ip]
```

## Query (with credentials)

```
ms-sql-config
```

### Identification and Scans <a href="identification-and-scans" id="identification-and-scans"></a>

```bash
# Queries Microsoft SQL Server (ms-sql) instances for a list of databases, linked servers,and configuration settings.
# Credentials required
ms-sql-config

# Queries the Microsoft SQL Browser service for the DAC (Dedicated AdminConnection)
ms-sql-dac

# Dumps the password hashes from an MS-SQL server in a format suitable
# Credentials required
ms-sql-dump-hashes

# Attempts to authenticate to Microsoft SQL Servers using an empty password forthe sysadmin (sa) account.
ms-sql-empty-password

# Queries Microsoft SQL Server (ms-sql) instances for a list of databases a user hasaccess to.
# Credentials required
ms-sql-hasdbaccess

# Attempts to determine configuration and version information for Microsoft SQLServer instances
ms-sql-info

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
