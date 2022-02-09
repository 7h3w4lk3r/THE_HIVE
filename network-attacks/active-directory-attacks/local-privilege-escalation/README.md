# ⭕ Privilege Escalation

## <mark style="color:red;">know before you start</mark>

**1. first rule of privilege escalation is that you HAVE TO be familiar with target system command line, actually the more comfortable you are with the shell the better and easier will be the privesc phase.**

**2. DO NOT jump to kernel exploits FIRST. yes, yes, in a CTF its a low-hanging fruit but in a real life scenario you dont want to crash the system SPECIALLY web and file servers, do you?**

**3. whatever tool you use for prives or whats called the post-exploitation information gathering, remember you should use manual techniques too. make sure you didnt miss anything.**

**4. all privilege escalations are effectively examples of access control violations. so if you can access what you want, there is usually no need to gain a root shell, except in a CTF or when you're trying to impress your client.**

## <mark style="color:red;">**Resources**</mark>

{% embed url="https://lolbas-project.github.io/#" %}

{% embed url="https://book.hacktricks.xyz/windows/windows-local-privilege-escalation" %}

{% embed url="https://www.fuzzysecurity.com/tutorials/16.html" %}

{% embed url="https://joshruppe.com/basic-windows-enumeration/" %}

{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md" %}

## <mark style="color:red;">Automated Tools</mark> <a href="#automated-tools" id="automated-tools"></a>

a list of automated tools used in post-exploitation enumeration and privilege escalation.

{% hint style="info" %}
unlike linux, in windows you have to consider the possibility that the any tool you are going to use may trigger a defensive mechanism such as AVs or an HIPS/HIDS so for operational security, its better to use obfuscated versions of these tools because even the harmless scripts such as powerview will be detected by most AV vendors out there.

the other reasonable way to stay under the radar in a real-world pentest is to use commands and tools that are already available on the target system such as wmic and powershell cmdlets. but keep in mind that in some environments with strict access control/delegation even running any powershell commands might trigger a sensor.

**as far as opsec is concerned, non of the tools mentioned bellow can evade AV products by default, mainly because of their signatures.**
{% endhint %}

{% embed url="https://github.com/AlessandroZ/BeRoot" %}

{% embed url="https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS" %}

{% embed url="https://github.com/hfiref0x/UACME" %}

{% embed url="https://github.com/PowerShellMafia/PowerSploit/" %}

{% embed url="https://github.com/411Hall/JAWS" %}

{% embed url="https://github.com/GhostPack/SharpUp" %}

{% embed url="https://github.com/rasta-mouse/Sherlock" %}

{% embed url="https://github.com/absolomb/WindowsEnum" %}

{% embed url="https://github.com/M4ximuss/Powerless" %}

{% embed url="https://github.com/GhostPack/Seatbelt" %}

{% embed url="https://github.com/itm4n/PrivescCheck" %}

***
