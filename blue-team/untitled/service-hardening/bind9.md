# BIND9

## <mark style="color:red;">Make sure the Bind is running with non-root account</mark>

```
ps aux | grep bind | grep -v '^root'
```

## <mark style="color:red;">Set Proper Permissions and Ownership</mark>&#x20;

```
chown -R root:bind /etc/bind
chown root:bind /etc/bind/named.conf*
chmod 640 /etc/bind/named.conf*
```

## <mark style="color:red;">Change / Remote Version Banner</mark>

Edit using VI, the file `/etc/bind/named.conf.options` and add the following settings under the `“Options”` section:

Add the line below to replace DNS version banner:

```
version "Secured DNS server";
```

In order to test, run the command below:

```
dig +short @localhost version.bind chaos txt
```



## <mark style="color:red;">Set Query & zone transfer Restrictions</mark>

Edit using VI, the file /etc/bind/named.conf.options and add the following settings under the “Options” section:

#### <mark style="color:green;">Add the line below to restrict recursive queries to trusted clients:</mark>

```
allow-recursion { localhost; 192.168.0.0/24; };
```

Replace 192.168.0.0/24 with the trusted internal segments and subnet mask.

In-order to test, run the command below:

```
nslookup www.google.com
```

#### <mark style="color:green;">Add the line below to restrict query origins to trusted clients:</mark>

```
allow-query { localhost; 192.168.0.0/24; };
```

Replace 192.168.0.0/24 with the trusted internal segments and subnet mask.

#### <mark style="color:green;">Add the line below to Nameserver ID:</mark>

```
server-id none;
```

#### <mark style="color:green;">Add the line below to restrict which hosts can perform zone transfers:</mark>

```
allow-transfer { 192.168.1.1; };
```

Replace 192.168.1.1 with the trusted DNS server.

#### <mark style="color:green;">Add the line below to restrict the DNS server to listen to specific interfaces:</mark>

```
listen-on port 53 { 127.0.0.1; 192.168.1.1; };
```

Replace 192.168.1.1 with the IP address of the DNS server.

Restart the DNS daemon:

```
service bind9 restart
```

## <mark style="color:red;">Setup DNSSEC</mark>

{% embed url="https://www.linuxmaker.com/en/linux/secure-bind9-with-dnssec/configuration-of-dnssec.html" %}
