# Evil-WinRM

A post exploitation framework for windows/AD pentesting using WinRM management protocol.

{% embed url="https://github.com/Hackplayers/evil-winrm" %}

#### <mark style="color:green;">Features:</mark>

* Compatible to Linux and Windows client systems
* Load in memory Powershell scripts
* Load in memory dll files bypassing some AVs
* Load in memory C# (C Sharp) assemblies bypassing some AVs
* Load x64 payloads generated with awesome [donut](https://github.com/TheWover/donut) technique
* Dynamic AMSI Bypass to avoid AV signatures
* Pass-the-hash support
* Kerberos auth support
* SSL and certificates support
* Upload and download files showing progress bar
* List remote machine services without privileges

### <mark style="color:orange;">Basic Usage</mark>

connect to target:

```
evil-winrm -u user -p password [ip]
```

{% hint style="info" %}
The shell is interactive and you can run system commands.
{% endhint %}

see options:

```
> menu
```

upload/download :&#x20;

```
upload/download [src] [dst]
```
