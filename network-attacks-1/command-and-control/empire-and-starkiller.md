# ⭕ Empire & Starkiller

## <mark style="color:red;">Intro</mark>

Empire is a post-exploitation framework that includes a pure-PowerShell2.0 Windows agent, and a pure Python 2.6/2.7 Linux/OS X agent. It is the merge of the previous PowerShell Empire and Python EmPyre projects.

{% embed url="https://github.com/EmpireProject/Empire" %}

## <mark style="color:red;">Installation</mark>

on kali and parrot:

```
apt install powershell-empire
```

other distro:

```
git clone https://github.com/EmpireProject/Empire.git

sudo ./setup/install.sh

# or use docker image:
docker pull empireproject/empire
```

## <mark style="color:red;">Basic Usage</mark>

* **agents** - Will allow you to jump to agents menu.
* **back & main** – Will take you back to the main menu.
* **exit** – Will exit from Empire.
* **help** – Will display help menu as shown in the above image.
* **info** – Will display information about the active listener.
* **kill** – Will kill a particular listener.
* **launcher** – Used to generate an initial launcher for a listener.
* **list** – Will list all the active listeners.
* **usestager** – Used to use a stager (we will see below what exactly is a stager).
* **uselistener** – Used to start a listener module.

run empire server and client:

```
powershell-empire server
powershell-empire client
```

list/use listeners:

```
(Empire) > listeners
(Empire: listeners) > uselistener meterpreter
(Empire: listeners/meterpreter) > info
```

set listener options and start it:

```
(Empire: listeners/meterpreter) > set name meterp
(Empire: listeners/meterpreter) > set port 5555
(Empire: listeners/meterpreter) > execute
```

list/use stagers:

```
(Empire) > usestager
(Empire) > usestager windows/launcher_bat
(Empire: stager/windows/launcher_bat) > set Listener meterpreter
(Empire: stager/windows/launcher_bat) > execute
        [*] Stager output written out to: /tmp/launcher.bat
```

send the payload and wait for agents:

```
(Empire) > agents
(Empire: agents) >  list
(Empire: agents) > interact [agent id]
```

use post-exploitation modules:

```
(Empire: agents) > usemodule
(Empire: agents) > usemodule external/generate_agent
(Empire: external/generate_agent) > options
Empire: external/generate_agent) > set Listener http
(Empire: external/generate_agent) > set Language powershell
(Empire: external/generate_agent) > execute
```

## <mark style="color:red;">Starkiller</mark>

Graphical interface for empire client:

install and run starkiller:

```
apt install starkiller
powershell-empire server
starkiller
```

default credentials:

```
username: empireadmin
 password: password123
```

![](<../../.gitbook/assets/image (23).png>)

## <mark style="color:red;">In-Depth Usage</mark>

{% embed url="https://www.securitynik.com/2022/02/beginning-powershell-empire-attack-in.html" %}

{% embed url="https://www.bc-security.org/post/empire-dropbox-c2-listener/" %}

{% embed url="https://www.bc-security.org/post/empire-4-2/" %}

{% embed url="https://blogs.keysight.com/blogs/tech/nwvs.entry.html/2021/06/16/empire_c2_-_networki-C4rq.html" %}

{% embed url="https://s3cur3th1ssh1t.github.io/Customizing_C2_Frameworks/" %}

{% embed url="https://rafalharazinski.gitbook.io/security/oscp/untitled-1/client-side-attack/empire-macro" %}

{% embed url="https://enigma0x3.net/2016/01/28/an-empire-case-study/" %}
