# mimipinguin

A tool to dump the login password from the current linux desktop user. Adapted from the idea behind the popular Windows tool mimikatz. This was assigned _CVE-2018-20781_ ([https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-20781](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-20781)).

MimiPenguin works similarly to the well-known “mimikatz” for Windows, but is designed for Linux and attempts to dump clear text credentials from memory from the following applications:

1. GDM password (Kali Desktop, Debian Desktop)
2. Gnome Keyring (Ubuntu Desktop, ArchLinux Desktop)
3. VSFTPd (Active FTP Connections)
4. Apache2 (Active HTTP Basic Auth Sessions)
5. OpenSSH (Active SSH Sessions - Sudo Usage)

There are two different scripts available, a shell script, and a python script. Both have their pros and cons; some features are supported in the python script, others not in the shell script, etc. We recommend trying both when assessing the target.

{% embed url="https://github.com/huntergregal/mimipenguin" %}

![](<../../../.gitbook/assets/1 (17).png>)

#### <mark style="color:green;">\*An "X" denotes full support while a "\~" denotes a feature with known bugs.</mark>

Once we’ve uploaded and executed the MimiPenguin scripts on our target system, we can see that they were both successful at obtaining the root password, either from the Gnome Keyring or the Gnome Display Manager in this case

We can also see that regardless of password complexity, we can still obtain users’ credentials with this tool.

![](<../../../.gitbook/assets/1 (10).png>)
