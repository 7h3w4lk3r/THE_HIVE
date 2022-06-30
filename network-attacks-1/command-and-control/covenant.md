# ⭕ Covenant

## <mark style="color:red;">Intro</mark>

{% embed url="https://github.com/cobbr/Covenant.git" %}

#### Covenant is a .NET command and control framework that aims to:

* highlight the attack surface of .NET
* makes the use of offensive .NET tradecraft easier
* serve as a collaborative C2 platform for red teamers

#### Covenant offers these key features:

* versatile : cross-platform, multi-user, intuitive web interface
* built-in implants for .NET framework and .NET Core
* large library of built-in tasks that utilize SharpSploit/GhostPack
* customizable: custom tasks, C2 profiles, implants
* &#x20;tracks indicators and credentials captured
* peer-to-peer C2

#### Use Cases:

* more control over post-ex
* using pure .NET framework implants (.NET 3.5/4.0)
* .NET Core implants
* more granular control over C2 structure
* easily change implants or indicators

## <mark style="color:red;">Installation</mark>

{% embed url="https://github.com/cobbr/Covenant/wiki/Installation-And-Startup" %}

````
git clone --recurse-submodules https://github.com/cobbr/Covenant
cd Covenant/Covenant
~/Covenant/Covenant > dotnet run

```
Covenant has started! Navigate to https://127.0.0.1:7443 in a browser
```
````

{% hint style="info" %}
If you are using the docker installer, you might encounter some issues with reverse shells.
{% endhint %}

after opening the link in the browser you see this:

![](<../../.gitbook/assets/image (200).png>)

#### there are different tabs in the web console:

* **Listeners :** create listeners
* **Launchers :** create a payload/one-liner for your listener
* **grunts :** compromised systems will show up here
* &#x20;templates : create new templates for implants
* **tasks :** create new tasks done by .NET framework
* **taskings :** grunts tasking list
* **graph :** a simple graph showing grunts. C2 and listener types as nodes
* **data :** loots will show up here
* **users :** red team users connected to this covenant server instance
