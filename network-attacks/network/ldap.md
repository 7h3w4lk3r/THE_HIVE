---
description: (TCP/UDP 389, TCP 636, 3268, 3269)
---

# 🟨 LDAP

## :information\_source: <mark style="color:blue;">Introduction</mark>

#### [Lightweight Directory Access Protocol](https://datatracker.ietf.org/doc/html/rfc4511)

Lightweight Directory Access Protocol (LDAP) is an internet protocol works on TCP/IP, used to access information from directories. LDAP protocol is basically used to access an active directory.

#### LDAP uses these ports:

* LDAP TCP/UDP 389
* LDAP SSL TCP 636
* LDAP GC TCP 3268
* LDAP GC SSL TCP 3269

## <mark style="color:red;">Features of LDAP</mark>

1. Functional model of LDAP is simpler due to this it omits duplicate, rarely used and esoteric feature.
2. It is easier to understand and implement.
3. It uses strings to represent data

### <mark style="color:orange;">Directories</mark>

Directories are set of object with similar attributes, organised in a logical and hierarchical manner. For example, Telephonic Directories. It is a distributed database application used to manage attributes in a directory.

![](<../../.gitbook/assets/image (45) (1).png>)

LDAP defines operations for accessing and modifying directory entries such as:

```
Searching for user specified criteria
Adding an entry
Deleting an entry
Modifying an entry
Modifying the distinguished name or relative distinguished name of an entry
Comparing an entry
```

## <mark style="color:red;">LDAP Models</mark>

​​LDAP can be explained by using four models upon which it based:

#### <mark style="color:orange;">Information Model:</mark>

This model describes structure of information stored in an LDAP Directory.In this basic information is stored in directory is called an entity. Entries here represents object of interest in real world such as people, server, organization, etc. Entries contain collection of attributes that contain information about object.Every attribute has a type and one or more values. Here types of attribute is associated with syntax and syntax specifies what kind of values can be stored

#### <mark style="color:orange;">Naming Model:</mark>

This model describes how information in an LDAP Directory is organized and identified. In this entries are organized in a Tree-Like structure called Directory Information Tree (DIT). Entries are arranged within DIT based on their distinguished name DN. DN is a unique name that unambiguously identifies a single entry.

#### <mark style="color:orange;">Functional Model:</mark>

LDAP defines operations for accessing and modifying directory entries . In this we discuss about LDAP operations in a programming language independent manner LDAP operations can be divided into following categories:

• Query

• Update

• Authentication

### <mark style="color:orange;">Security Model:</mark>

This model describes how information in LDAP directory can be protected from unauthorized access. It is based on BIND operation. There are several bind operation can be performed.

## <mark style="color:red;">LDAP Enumeration</mark>

### <mark style="color:orange;">LDAP anonymous binding</mark>

```
nmap -n -sV --script "ldap* and not brute" -p 389 192.168.56.111
ldapsearch -x -H ldap://192.168.56.111  
```

### <mark style="color:orange;">enumeration with credentials</mark>

<pre><code>pip3 install ldapdomaindump
ldapdomaindump &#x3C;IP> [-r &#x3C;IP>] -u '&#x3C;domain>\&#x3C;username>' -p '&#x3C;password>' [--authtype SIMPLE] --no-json --no-grep [-o /path/dir]


ldapdomaindump -u DOMAIN\\username -p [password or ntlm hash] -d domain.local


<strong>ldapsearch -x -H ldap://&#x3C;IP address>  -D '&#x3C;DOMAIN>\&#x3C;username>' -w '&#x3C;password>' -b "DC=&#x3C;1_SUBDOMAIN>,DC=&#x3C;TLD>"
</strong><strong>
</strong><strong>
</strong>impacket-GetADUsers -all lab.local/administrator -dc-ip 192.168.56.40



ldapsearch -x -H ldap://&#x3C;IP> -D '&#x3C;DOMAIN>\&#x3C;username>' -w '&#x3C;password>' -b "DC=&#x3C;1_SUBDOMAIN>,DC=&#x3C;TLD>"
-x Simple Authentication
-H LDAP Server
-D My User
-w My password
-b Base site, all data from here will be given
<strong>
</strong><strong>
</strong></code></pre>

Finding all objects in the directory tree:

```
ldapsearch -x -H ldap://192.168.56.111 -b "dc=lab,dc=local" "objectclass=*"
```

Extract users:

```
ldapsearch -x -H ldap://<IP> -D '<DOMAIN>\<username>' -w '<password>' -b "CN=Users,DC=<1_SUBDOMAIN>,DC=<TLD>"

ldapsearch -x -H ldap://192.168.56.111 -b "dc=lab,dc=local" "objectclass=user"

ldapsearch -x -H ldap://192.168.56.111 -b "dc=lab,dc=local" sAMAccountName | grep sAMAccountName | awk -F ": " '{print $2}'
```

Extract computers:

```
ldapsearch -x -H ldap://<IP> -D '<DOMAIN>\<username>' -w '<password>' -b "CN=Computers,DC=<1_SUBDOMAIN>,DC=<TLD>"
```

Extract Domain Admins:

```
ldapsearch -x -H ldap://<IP> -D '<DOMAIN>\<username>' -w '<password>' -b "CN=Domain Admins,CN=Users,DC=<1_SUBDOMAIN>,DC=<TLD>"
```

Extract Enterprise Admins:

```
ldapsearch -x -H ldap://<IP> -D '<DOMAIN>\<username>' -w '<password>' -b "CN=Enterprise Admins,CN=Users,DC=<1_SUBDOMAIN>,DC=<TLD>"
```

### <mark style="color:orange;">Bypass TLS SNI check</mark>

Windows Server < 2003

just by accessing the LDAP server with an arbitrary domain name (like company.com) he was able to contact the LDAP service and extract information as an anonymous user:

```
ldapsearch -H ldaps://ip:636/ -x -s base -b '' "(objectClass=*)" "*" +

ldapsearch -H ldap://ip:389/ -x -s base -b '' "(objectClass=*)" "*" +
```

### <mark style="color:orange;">crackmapexec</mark>

```
# list PKIs/CAs
cme ldap "domain_controller" -d "domain" -u "user" -p "password" -M adcs
​
# list subnets referenced in AD-SS
cme ldap "domain_controller" -d "domain" -u "user" -p "password" -M subnets
​
# machine account quota
cme ldap "domain_controller" -d "domain" -u "user" -p "password" -M maq
​
# users description
cme ldap "domain_controller" -d "domain" -u "user" -p "password" -M get-desc-users


crackmapexec ldap <IP> -u <User> -p <Password> --kdcHost <Host> --admin-count

# ASREPROAST
crackmapexec ldap <IP> -u <User> -p <Password> --kdcHost <Host>  --asreproast 
crackmapexec ldap <IP> -u <User> -p <Password> --kdcHost <Host>  --groups

# KERBEROASTING
crackmapexec ldap'<IP> -u <User> -p <Password> --kdcHost <Host>  --kerberoasting 
crackmapexec ldap <IP> -u <User> -p <Password> --kdcHost <Host>  --password-not-required
crackmapexec ldap <IP> -u <User> -p <Password> --kdcHost <Host>  --trusted-for-delegation
crackmapexec ldap <IP> -u <User> -p <Password> --kdcHost <Host>  --users
​
# Modules
crackmapexec ldap <IP> -u <User> -p <Password> --kdcHost <Host> -M get-desc-users
crackmapexec ldap <IP> -u <User> -p <Password> --kdcHost <Host> -M laps
crackmapexec ldap <IP> -u <User> -p <Password> --kdcHost <Host> -M ldap-signing
```
