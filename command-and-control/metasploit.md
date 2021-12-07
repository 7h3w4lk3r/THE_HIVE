# Metasploit

![](<../.gitbook/assets/image (282).png>)

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

load/unload a plugin load \[plugin] unload

load a third party module loadpath \[path/to/module]

run resource(batch) files resource \[file] usefull in karmetasploit attacks and alone batch files can speedup a test by automating tasks.

route sockets through a session or comm route -h

show/select active sessions sessions -l sessions \[num]

set/unset options set unset

set/unset global variables setg \[name] \[value] unsetg

save state save

show available options/modules show options show targets show adanced show payloads show exploits show auxiliary show encoders show nops show evation

run and send an exploit to background exploit -j





























