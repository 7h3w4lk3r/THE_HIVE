# vsftpd

## <mark style="color:red;">Enable Local User Account</mark>

To enable a local user account for FTP access, make the following changes to the `/etc/vsftpd.conf` file:

```
/etc/vsftpd.conf
anonymous_enable=NO
connect_from_port_20=NO
local_enable=YES
write_enable=YES
```

## <mark style="color:red;">Enable chroot Jail</mark>

Open `/etc/vsftpd/vsftpd.conf` and uncomment the following options:

```
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd.chroot_list
```

The file `/etc/vsftpd.chroot_list` contains the list of jailed users one per line.

Save the files and restart your service.

```
systemctl restart vsftpd
```

## <mark style="color:red;">Anonymous Only</mark>

This kind of FTP server is useful if your files should be available for users without any passwords or logins.

Open `/etc/vsftpd/vsftpd.conf` file, and change the following options with the corresponding values.

```
listen=NO
listen_ipv6=NO
anonymous_enable=YES
local_enable=NO
write_enable=NO
```

Then we need to create a non-privileged system account to use it for anonymous FTP-type access.

```
useradd -c " FTP User" -d /var/ftp -r -s /sbin/nologin ftp
```

restart the service:

```
service vsftpd restart
```

This user has no privileges on the system, so it is safer to use it when accessing an FTP server.

#### generate a certificate request using the openssl command:

```
openssl genrsa -des3 -out FTP.key
```

#### Then we generate a certificate request:

```
openssl req -new -key FTP.key -out certificate.csr
```

#### Now we remove the password from the key file:

```
cp FTP.key FTP.key.orig
openssl rsa -in FTP.key.orig -out ftp.key
```

#### generate our certificate:

```
openssl x509 -req -days 365 -in certificate.csr -signkey ftp.key -out mycertificate.crt
```

**Now we copy the certificate file and the key and to `/etc/pki/tls/certs`:**

```
cp ftp.key /etc/pki/tls/certs/
cp mycertificate.crt /etc/pki/tls/certs
```

#### configure vsftpd to support secure connections.

Open `/etc/vsftpd/vsftpd.conf` file and add the following lines:

```
ssl_enable=YES
allow_anon_ssl=YES
ssl_tlsv1=YES
ssl_sslv2=NO
ssl_sslv3=NO
rsa_cert_file=/etc/pki/tls/certs/mycertificate.crt
rsa_private_key_file=/etc/pki/tls/certs/ftp.key
ssl_ciphers=HIGH
require_ssl_reuse=NO
```

Restart your service to reflect these changes

Try to connect to your FTP server from any client on any system like Windows and choose the secured connection or FTPS, and you will successfully see your folders.

## <mark style="color:red;">SFTP vs. FTPS</mark>

In the last example, we saw the FTP over the SSL layer (FTPS), and we’ve successfully connected to the FTP server. However, with the tightly secured firewall, it is difficult to manage this kind of connection since FTPS uses multiple port numbers.

The best solution, in this case, is to use SFTP (FTP over SSH).SFTP uses port 22 only.

The FTP server will use this port for all connections during FTP sessions.

If you are using a firewall, you should choose SFTP, since it needs only one port.
