# ⭕ SMB

## <mark style="color:red;">Windows</mark>

### <mark style="color:orange;">SMB v1</mark>

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

### <mark style="color:orange;">SMB v2/v3</mark>

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

## <mark style="color:red;">SMB Server on Linux</mark>

### <mark style="color:orange;">Using Impacket</mark>

the format is: impacket-smbserver \[share name to use] \[share directory path]

```
# python3 /usr/share/doc/python3-impacket/examples/smbserver.py tools .
# python /usr/share/doc/python-impacket/examples/smbserver.py tools .
```

### <mark style="color:orange;">Using SAMBA</mark>

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

## <mark style="color:red;">SMB Client on Windows</mark>

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
