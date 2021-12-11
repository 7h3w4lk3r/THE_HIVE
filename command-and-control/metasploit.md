# 🔧 Metasploit

![](<../.gitbook/assets/image (282) (1) (1) (1).png>)

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

![](<../.gitbook/assets/image (289).png>)

create a payload with msfvenom and set the `lhost` to the union address and append .link to it:

![](<../.gitbook/assets/image (272).png>)

in listener set lhost to your local ip address:

![](<../.gitbook/assets/image (275).png>)

run the attack:

![](<../.gitbook/assets/image (291).png>)

using tor is extremely slow ad your session may timeout, play around with timeout values.

## Logging&#x20;

every command will be logged in a file named console.log in `~/.msf4/logs`

```
se consolelogging true
```





