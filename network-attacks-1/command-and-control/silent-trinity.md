# ⭕ Silent Trinity

## <mark style="color:red;">Intro</mark>

> SILENTTRINITY is a modern, asynchronous, multiplayer & multiserver C2/post-exploitation framework. It's the culmination of an extensive amount of research into using embedded third-party .NET scripting languages to dynamically call .NET API's, a technique the author coined as BYOI (Bring Your Own Interpreter). The aim of this tool and the BYOI concept is to shift the paradigm back to PowerShell style like attacks (as it offers much more flexibility over traditional C# tradecraft) only without using PowerShell in anyway.

#### Using it is pretty much like powershell empire.

{% embed url="https://github.com/byt3bl33d3r/SILENTTRINITY" %}

{% embed url="https://github.com/byt3bl33d3r/SILENTTRINITY/wiki" %}

#### Some of the main features that distinguish SILENTTRINITY are:

* **Multi-User & Multi-Server** - Supports multi-user collaboration. Additionally, the client can connect to and control multiple Teamservers.
* **Client and Teamserver Built in Python 3.7** - Latest and greatest features of the Python language are used, heavy use of Asyncio provides ludicrous speeds.
* **Real-time Updates and Communication** - Use of Websockets allow for real-time communication and updates between the Client and Teamserver.
* **Focus on Usability with an Extremely Modern CLI** - Powered by [prompt-toolkit](https://github.com/prompt-toolkit/python-prompt-toolkit).
* **Dynamic Evaluation/Compilation Using .NET Scripting Languages** - The SILENTTRINITY implant [Naga](https://github.com/byt3bl33d3r/Naga), is somewhat unique as it uses embedded third-party .NET scripting languages (e.g. [Boolang](https://github.com/boo-lang/boo)) to dynamically compile/evaluate tasks, this removes the need to compile tasks server side, allows for real-time editing of modules, provides greater flexibilty and stealth over traditional C# based payloads and makes everything much more light-weight.
* **ECDHE Encrypted C2 Communication** - SILENTTRINITY uses Ephemeral Elliptic Curve Diffie-Hellman Key Exchange to encrypt all C2 traffic between the Teamserver and its implant.
* **Fully Modular** - Listeners, Modules, Stagers and C2 Channels are fully modular allowing operators to easily build their own.
* **Extensive logging** - Every action is logged to a file.
* **Future proof** - HTTPS/HTTP listeners are built on [Quart](https://gitlab.com/pgjones/quart) & [Hypercorn](https://gitlab.com/pgjones/hypercorn) which also support HTTP2 & Websockets.

## <mark style="color:red;">Installation</mark>

#### native install:

```
git clone https://github.com/byt3bl33d3r/SILENTTRINITY
cd SILENTTRINITY
python3 -m pip install -r requirements.txt
python3 st.py -h
```

#### using pipenv:

```
git clone https://github.com/byt3bl33d3r/SILENTTRINITY
pip3 install --user pipenv && pipenv install && pipenv shell
python3 st.py -h
```

#### Install required python modules (optional):

```
sudo pip3 install netifaces && sudo pip3 install docopt && sudo pip3 install defusedxml && sudo pip3 install websockets && sudo pip3 install aiosqlite && sudo pip3 install termcolor
```

### <mark style="color:orange;">Server</mark>

#### Start the application, use the IP of the server in which the client should connect, and a password:

```
python3 st.py teamserver 192.168.0.19 Pass
```

{% hint style="info" %}
It is important to note the Teamserver certificate, and compare it with the one the client gets. At this point in time we are waiting for a valid request to come in from a valid client.
{% endhint %}

#### Run netstat to verify the server is listening on the specified port, in this case we are using the default 5000

```
ss -ant | grep 5000
```

### <mark style="color:orange;">Client</mark>

#### Run the application as client

```
python3 st.py client
```

#### connect to server:

```
teamserver
connect wss://user1:Pass@192.168.0.19:5000
```

## <mark style="color:red;">Basic Usage</mark>

### <mark style="color:orange;">Listeners</mark>

#### go to listeners menu:

```
listeners
```

#### silent trinity has 3 types of listeners:

* wmi
* http
* https

#### choose and start a listener:

```
ST (listeners) ≫ use https
ST (listeners)(https) ≫ set Name test-listener
ST (listeners)(http) ≫ set BindIP 127.0.0.1
ST (listeners)(http) ≫ set Port 8888
ST (listeners)(http) ≫ start
```

### <mark style="color:orange;">Stagers</mark>

#### go to stagers tab:

```
stagers
list
```

#### silent trinity has the following stagers:

* msbuild (XML inline C# task)
* exe
* csharp
* wmic (XSL execution)
* dll
* powershell script

#### choose a stager :

```
ST (stagers) ≫ use powershell
ST (stagers)(powershell) ≫ generate test-listener
```

### <mark style="color:orange;">Post-exploitation</mark>

#### list sessions:

```
ST (stagers)(powershell) ≫ sessions
ST (sessions) ≫list
```

#### select a post-exploitation module to use:

```
ST (sessions) ≫ modules
ST (modules) ≫ list
```

example:

```
 ST (modules) ≫ use boo/shell
  ST (modules)(boo/shell) ≫ options
  ST (modules)(boo/shell) ≫ set command whoami 
   run [session ID]
```

## <mark style="color:red;">In-Depth Usage</mark>&#x20;

{% embed url="https://www.hackingarticles.in/command-control-silenttrinity-post-exploitation-agent/" %}

{% embed url="https://www.jamescarroll.me/blog/introduction-to-silenttrinity-tutorial-2020" %}

{% embed url="https://www.youtube.com/watch?v=0_b3A1SOyVw" %}
