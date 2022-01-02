# 🔧 Process Hardening

## <mark style="color:red;">Restrict Core Dumps</mark>

A core dump is the memory of an executable program. It is generally used to determine why a program aborted. It can also be used to glean confidential information from a core file. The system provides the ability to set a soft limit for core dumps, but this can be overridden by the user.

Setting a hard limit on core dumps prevents users from overriding the soft variable. If core dumps are required, consider setting limits for user groups (see limits.conf(5) ). In addition, setting the fs.suid\_dumpable variable to 0 will prevent setuid programs from dumping core.

Run the following commands and verify output matches:

```
# grep "hard core" /etc/security/limits.conf /etc/security/limits.d/*
* hard core 0

# sysctl fs.suid_dumpable
fs.suid_dumpable = 0

# grep "fs\.suid_dumpable" /etc/sysctl.conf /etc/sysctl.d/*
fs.suid_dumpable = 0
```

Run the following command to check if systemd-coredump is installed:

```
# systemctl is-enabled coredump.service
```

if enabled or disabled is returned systemd-coredump is installed

Add the following line to /etc/security/limits.conf or a /etc/security/limits.d/\* file:

```
* hard core 0
```

Set the following parameter in /etc/sysctl.conf or a /etc/sysctl.d/\* file:

```
fs.suid_dumpable = 0
```

Run the following command to set the active kernel parameter:

```
sysctl -w fs.suid_dumpable=0
```

If systemd-coredump is installed:&#x20;

edit `/etc/systemd/coredump.conf` and add/modify the following lines:

```
Storage=none
ProcessSizeMax=0
```

Run the command:

```
systemctl daemon-reload
```

## <mark style="color:red;">Enable Exploit Mitigation Mechanisms</mark>

### <mark style="color:orange;">ASLR</mark>

Set the following parameter in `/etc/sysctl.conf` or a `/etc/sysctl.d/*` file:&#x20;

```
kernel.randomize_va_space = 2 
```

Run the following command to set the active kernel parameter:

```
sysctl -w kernel.randomize_va_space=2
```

## <mark style="color:red;">Disable Prelink</mark>

prelinkis a program that modifies ELF shared libraries and ELF dynamically linked binaries in such a way that the time needed for the dynamic linker to perform relocations at startup significantly decreases.

{% hint style="info" %}
The prelinking feature can interfere with the operation of AIDE, because it changes binaries. Prelinking can also increase the vulnerability of the system if a malicious user is able to compromise a common library such as libc
{% endhint %}

Verify prelink is not installed. Depending on the package management in use one of the following commands may provide the needed information:

```
# rpm -q prelink
# dpkg -s prelink
```

Run the following command to restore binaries to normal:

```
prelink -ua
```

Uninstall prelink using the appropriate package manager or manual installation:

```
# yum remove prelink
# apt-get remove prelink
# zypper remove prelink
```

## <mark style="color:red;">Kernel Parameters</mark>







