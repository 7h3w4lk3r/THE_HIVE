# RDP Session Hijacking

## <mark style="color:red;">Intro</mark>

RDP hijacking attacks involve the attacker “resuming” a previously disconnected RDP session. This allows the attacker to get into a privileged system without having to steal the user’s credentials. For example, if an administrator remoted into a Windows Server machine a few days ago, it is much easier for the attacker to “resume” this very session, rather than attempting to obtain the administrator account’s password.

## <mark style="color:red;">Passwordless hijacking</mark>

Let’s focus on the RDP hijacking technique leveraging the [Tscon.exe](https://support.microsoft.com/en-us/help/302801/the-use-of-tscon-exe-can-leave-a-previously-locked-console-unlocked) utility, which comes with Windows. It enables a user to connect to a different remote desktop session on a system or switch between different sessions.

The syntax for the command is simple:

```powershell
tscon {<SessionID> | <SessionName>} [/dest:<SessionName>] [/password:<pw> | /password:*] [/v]
```

The simplest example would be `tscon 2`.

&#x20;Running such a command on a server hosting the remote desktop session would connect the user to session with ID 2 and disconnect any existing sessions they are on. ****&#x20;

to resume a previously disconnected RDP session an attacker with system-level access can setup a service with this syntax:

```powershell
sc create hijackedsession binpath= “cmd.exe /k tscon 1 /dest:rdp-tcp#2”
net start hijackedsession

```

This will disconnect the current session of the attacker and “resume” the previously disconnected session between the attacker and the RDP server without asking for a password or leaving much of a forensic trace.

## <mark style="color:red;">Mitigation</mark>

Due to the nature of RDP protocol and the behavior exploited by this technique, monitoring for an RDP hijacking attack is difficult because, to forensic tools, the activity looks as if a legitimate, authorized remote user was accessing the system.

**Enforce Group Policy:** Instead of leaving “disconnected” remote desktop sessions in dormant state for long, Group Policy settings should be changed to log off users either instantly or shortly after they have disconnected an RDP session. This will prevent an attacker from simply “resuming” a session in a credential-less manner.
