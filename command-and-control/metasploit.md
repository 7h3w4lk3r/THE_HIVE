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























