# LDAP

Lightweight Directory Access Protocol \(LDAP\) is an internet protocol works on TCP/IP, used to access information from directories. LDAP protocol is basically used to access an active directory.

### Features of LDAP

Functional model of LDAP is simpler due to this it omits duplicate, rarely used and esoteric feature.

It is easier to understand and implement.

It uses strings to represent data



### Directories

Directories are set of object with similar attributes, organised in a logical and hierarchical manner. For example, Telephonic Directories. It is a distributed database application used to manage attributes in a directory.

![](../../.gitbook/assets/image%20%2843%29.png)

LDAP defines operations for accessing and modifying directory entries such as:

```text
Searching for user specified criteria
Adding an entry
Deleting an entry
Modifying an entry
Modifying the distinguished name or relative distinguished name of an entry
Comparing an entry
```

## LDAP Models

​​LDAP can be explained by using four models upon which it based:

#### Information Model:

This model describes structure of information stored in an LDAP Directory.In this basic information is stored in directory is called an entity. Entries here represents object of interest in real world such as people, server, organization, etc. Entries contain collection of attributes that contain information about object.Every attribute has a type and one or more values. Here types of attribute is associated with syntax and syntax specifies what kind of values can be stored

#### Naming Model:

This model describes how information in an LDAP Directory is organized and identified. In this entries are organized in a Tree-Like structure called Directory Information Tree \(DIT\). Entries are arranged within DIT based on their distinguished name DN. DN is a unique name that unambiguously identifies a single entry.

#### Functional Model:

LDAP defines operations for accessing and modifying directory entries . In this we discuss about LDAP operations in a programming language independent manner LDAP operations can be divided into following categories:

• Query 

• Update

 • Authentication

#### Security Model:

This model describes how information in LDAP directory can be protected from unauthorized access. It is based on BIND operation. There are several bind operation can be performed.

## Enumeration

#### You can try to enumerate a LDAP with or without credentials using python: pip3 install ldap3

First try to connect without credentials:

```text
>>> import ldap3
>>> server = ldap3.Server('x.X.x.X', get_info = ldap3.ALL, port =636, use_ssl = True)
>>> connection = ldap3.Connection(server)
>>> connection.bind()
True
>>> server.info
```

  
`` If the response is True like in the previous example, you can obtain some interesting data of the LDAP \(like the naming context or domain name\) server from:  


```text
>>> server.info
DSA info (from DSE):
Supported LDAP versions: 3
Naming contexts:
dc=DOMAIN,dc=DOMAIN
```

Once you have the naming context you can make some more exciting queries. This simply query should show you all the objects in the directory:

```text
>>> connection.search(search_base='DC=DOMAIN,DC=DOMAIN', search_filter='(&(objectClass=*))', search_scope='SUBTREE', attributes='*')
True
>> connection.entries 
```

Or dump the whole ldap:

```text
>> connection.search(search_base='DC=DOMAIN,DC=DOMAIN', search_filter='(&(objectClass=person))', search_scope='SUBTREE', attributes='userPassword')
True
>>> connection.entries 
```

#### for more information on LDAP NULL Bind exploitation check out this [link](https://www.n00py.io/2020/02/exploiting-ldap-server-null-bind/)



