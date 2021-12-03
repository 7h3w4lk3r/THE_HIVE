# SMB

## Windows

### SMB v1&#x20;

detect:

```
Get-WindowsOptionalFeature -Online -FeatureName smb1protocol
```

Disable:

```
Disable-WindowsOptionalFeature -Online -FeatureName smb1protocol
```

Enable (reboot required):

```
Enable-WindowsOptionalFeature -Online -FeatureName smb1protocol
Enable-WindowsOptionalFeature -Online -FeatureName "SMB1Protocol-Client" -All
```

### SMB v2/v3&#x20;

Detect:

```
Get-SmbServerConfiguration | Select EnableSMB2Protocol
```

Disable:

```
Set-SmbServerConfiguration -EnableSMB2Protocol $false
```

Enable:

```
Set-SmbServerConfiguration -EnableSMB2Protocol $true
```

## SMB Server on Linux

### Using Impacket

the format is: impacket-smbserver \[share name to use] \[share directory path]

```
# python3 /usr/share/doc/python3-impacket/examples/smbserver.py tools .
# python /usr/share/doc/python-impacket/examples/smbserver.py tools .
```

### Using SAMBA

```
apt-get install samba
mkdir /tmp/smb
chmod 777 /tmp/smb
#Add to the end of /etc/samba/smb.conf this:
[public]
    comment = Samba on Ubuntu
    path = /tmp/smb
    read only = no
    browsable = yes
    guest ok = Yes
#Start samba
service smbd restart
```

## SMB Client on Windows

#### to view available shares from windows

```
net view \\192.168.56.1
```

#### to view files available in a share from windows

```
dir \\192.168.56.1\share_name
```

#### To move files to Windows:

```
copy \\IP\share_name\file_name  path_to_save
> copy \\192.168.1.11\tools\file.ext file.ext
```

#### To copy files from to linux smb server:

```
> copy file.ext \\192.168.1.11\tools\file.ext
```
