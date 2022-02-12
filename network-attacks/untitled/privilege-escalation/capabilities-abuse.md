# Capabilities Abuse

## <mark style="color:red;">Intro</mark>

{% embed url="https://linux-audit.com/linux-capabilities-101" %}

## <mark style="color:red;">Scenario</mark>

{% embed url="https://blog.pentesteracademy.com/privilege-escalation-by-abusing-sys-ptrace-linux-capability-f6e6ad2a59cc" %}

{% embed url="https://book.hacktricks.xyz/linux-unix/privilege-escalation/linux-capabilities" %}

{% embed url="https://tbhaxor.com/exploiting-linux-capabilities-part-1" %}

Capabilities in Linux are special attributes that can be allocated to processes, binaries, services and users and they can allow them specific privileges that are normally reserved for root-level actions, such as being able to intercept network traffic or mount/unmount file systems. If misconfigured, these could allow an attacker to elevate their privileges to root.



## <mark style="color:red;">**Dangerous Capabilities**</mark>

The following capabilities are particularly dangerous and should be investigated further if found enabled on a system:

* CAP\_CHOWN
* CAP\_DAC\_OVERRIDE
* CAP\_DAC\_READ\_SEARCH
* CAP\_SETUID
* CAP\_SETGID
* CAP\_NET\_RAW
* CAP\_SYS\_ADMIN
* CAP\_SYS\_PTRACE
* CAP\_SYS\_MODULE
* CAP\_FORMER
* CAP\_SETFCAP

### <mark style="color:orange;">Find Binaries with Dangerous CAPs</mark>

The following command can be used to identify binaries that have capabilities allocated to them:

```
getcap -r / 2>/dev/null
```

![](<../../../.gitbook/assets/image (46) (1).png>)

Whereas the following command can be used to check whether a running process has capabilities assigned:

```
cat /proc/[process ID]/status | grep Cap
```

Capabilities assigned to users are stored in the /etc/security/capability.conf configuration file:

![](<../../../.gitbook/assets/image (1) (1).png>)

Additionally, systemd offers directives for configuring capabilities on service units, through the “AmbientCapabilities” variable:

![](<../../../.gitbook/assets/image (23) (1).png>)

### <mark style="color:orange;">LinPEAS</mark>

The easiest way to identify misconfigured capabilities is to use enumeration scripts such as LinPEAS:

![](<../../../.gitbook/assets/image (14) (1) (1) (1).png>)



Once the capabilities have been assigned, a great resource to find out if they can be vulnerable (if assigned to variables) is through [GTFOBins](http://gtfobins.github.io), as for each applicable binary it has a handy “Capabilities” section which shows how certain capabilities can be exploited to elevate privileges. This [HackTricks](https://book.hacktricks.xyz/linux-unix/privilege-escalation/linux-capabilities) page is also great. Alternatively, googling for the capability and the object it is assigned to normally does the trick.

## <mark style="color:red;">**Exploiting CAPs**</mark>

Based on the output from the commands used above, the /usr/bin/python3.8 binary has the cap\_setuid capabilities assigned, which allows to set the effective user ID of a process when running its binary i.e. executing binaries as root.

Aaccording to GTFOBins, it can be easily exploited with the following command, which simply executes /bin/sh with the SUID bit set:

```
/usr/bin/python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

![](<../../../.gitbook/assets/image (11) (1).png>)

Executing the command while logged in as a non-root user:

![](<../../../.gitbook/assets/image (24) (1).png>)

As shown above, this has allowed to escalate privileges to root, many different capabilities can be exploited to read/write to files, intercept network traffic, mount/unmount file systems and more, which can potentially lead to escalation of privileges.

## <mark style="color:red;">Useful Resources</mark>

{% embed url="https://wixnic.github.io/linux-privesc-capabilities" %}

{% embed url="https://materials.rangeforce.com/tutorial/2020/02/19/Linux-PrivEsc-Capabilities/index.html" %}
