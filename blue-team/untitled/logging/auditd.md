# auditd

{% embed url="https://logit.io/sources/configure/auditd" %}

The Linux auditd system hooks deeply into the kernel, giving it highly granular access to events happening on the system. You can track when files are accessed—whether that's executing a program, or just opening a file for reading or writing. More generally, auditd can track any system call (see `/usr/include/asm/unistd_64.h` for a list of system call names), including matching specific system call arguments and error codes. There's even a hook for capturing user keystrokes, though whether this is a feature or a danger is in the eye of the beholder.



### <mark style="color:orange;">Installation</mark>

One of the beauties of auditd is that it works at the Linux kernel level, rather than at the user-mode level. This makes it much harder for attackers to subvert. On Red Hat-type systems, auditd comes installed and enabled by default. So, you'll find it already there on your CentOS machine. On Ubuntu, it won't be installed, so you'll have to do it yourself:

```
sudo apt install auditd
sudo systemctl restart auditd
sudo service auditd restart
```

### <mark style="color:orange;">Creating audit rules</mark>

check to see whether any audit rules are in effect:

```
sudo auditctl -l
```

we want to see when someone changes the /etc/passwd file

```
sudo auditctl -w /etc/passwd -p wa -k passwd_changes
```

the rule is only temporary and will disappear when we reboot the machine. To make it permanent, we need to create a custom rules file in the `/etc/audit/rules.d/` directory. Then, when you restart the auditd daemon, the custom rules will be inserted into the /etc/audit/audit.rules file.

```
sudo less /etc/audit/audit.rules
```

![](<../../../.gitbook/assets/image (143).png>)

**-D :** This will cause all rules and watches that are currently in effect to be deleted so that we can start from a clean slate. So, if I were to restart the auditd daemon right now, it would read this audit.rules file, which would delete the rule that I just created.

**-b 8192 :** This sets the number of outstanding audit buffers that we can have going at one time. If all of the buffers get full, the system can't generate any more audit messages.

**-f 1 :** This sets the failure mode for critical errors, and the value can be either 0 , 1 , or 2 . -f 0 would set the mode to silent, meaning that auditd wouldn't do anything about critical errors. -f 1 , as we can see here, tells auditd to only report the critical errors, while -f 2 would cause the Linux kernel to go into panic mode. According to the auditctl man page, anyone in a high-security environment would likely want to change this to -f 2 . For our purposes, though, -f1 works.

You could use your text editor to create a new rules file in the /etc/audit/rules.d/ directory. Alternatively, you could just redirect the auditctl -l output into a new file, like this:

```
sudo sh -c "auditctl -l > /etc/audit/rules.d/custom.rules"
sudo systemctl restart auditd
```



## <mark style="color:red;">Redhat auditd</mark>

{% embed url="https://www.redhat.com/sysadmin/configure-linux-auditing-auditd" %}

{% embed url="https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/security_guide/sec-configuring_the_audit_service" %}

## <mark style="color:red;">Debian auditd</mark>

{% embed url="https://www.pinoylinux.org/system-adminstration/monitoring-file-access-using-auditd" %}

{% embed url="https://www.theurbanpenguin.com/installing-the-linux-audit-system-on-ubuntu-18-04" %}
